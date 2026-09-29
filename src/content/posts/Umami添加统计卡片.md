---
title: Firefly 添加 Umami 统计卡片
published: 2026-09-03
description: 在 Firefly 主题侧边栏集成 Umami 统计卡片，实时展示浏览量、访问数和游客数。
tags: [访客统计]
category: 值得一看
slug: fireflyumami
draft: false
---

# 前言

了解访客来源与流量趋势是必要的，开源、轻量、隐私友好的 Umami 往往是站长的首选。

直接将 Umami 的分享链接做成一个组件，挂在侧边栏，这样就很顺眼了。

# 获取分享链接

部署 Umami，添加站点，启用分享链接。

得到类似 `https://xxx.xxx.xxx/share/xxx` 的链接。

# 创建组件

文件路径：`src/components/widget/UmamiStats.astro`

```ts 
---
import WidgetLayout from "@/components/common/WidgetLayout.astro";

interface Props {
  class?: string;
  style?: string;
}
const { class: className, style } = Astro.props;
---

<WidgetLayout id="umami-stats" name="统计" class:list={["umami-stats-container", className, "cursor-pointer transition-opacity active:scale-95"]} {style}>
    <a target="_blank" rel="noopener noreferrer" class="block umami-link">
        <div class="text-center py-2">
            <div class="text-3xl font-bold text-neutral-900 dark:text-neutral-100 umami-total-pageviews">-</div>
            <div class="text-sm text-neutral-500 dark:text-neutral-400">总浏览量</div>
        </div>
        <div class="grid grid-cols-2 divide-x divide-neutral-200 dark:divide-neutral-700 text-center pt-2">
            <div class="px-2">
                <div class="text-xl font-bold text-neutral-900 dark:text-neutral-100 umami-total-visits">-</div>
                <div class="text-sm text-neutral-500 dark:text-neutral-400">访问数</div>
            </div>
            <div class="px-2">
                <div class="text-xl font-bold text-neutral-900 dark:text-neutral-100 umami-total-visitors">-</div>
                <div class="text-sm text-neutral-500 dark:text-neutral-400">游客数</div>
            </div>
        </div>
    </a>
</WidgetLayout>

<script>
const UMAMI_CONFIG = {
    shareUrl: 'https://xxx.xxx.xxx/share/xxx',
};

let __UMAMI_INTERNAL = {
    baseUrl: '',
    websiteId: '',
    shareToken: '',
    shareId: '',
    isReady: false
};

const FALLBACK_STATS = {
    pageviews: 1000,
    visits: 1000,
    visitors: 1000,
};

async function initUmamiConfig() {
    try {
        const sharePath = UMAMI_CONFIG.shareUrl.split('/share/')[1];
        if (!sharePath) throw new Error('Invalid Umami Share URL');

        let apiBase = '';
        if (UMAMI_CONFIG.shareUrl.includes('cloud.umami.is') || UMAMI_CONFIG.shareUrl.includes('analytics.umami.is')) {
            const region = UMAMI_CONFIG.shareUrl.includes('/analytics/eu/') ? 'eu' : 'us';
            apiBase = `https://cloud.umami.is/analytics/${region}/api`;
        } else {
            const urlObj = new URL(UMAMI_CONFIG.shareUrl);
            apiBase = `${urlObj.origin}/api`;
        }

        const res = await fetch(`${apiBase}/share/${sharePath}`);
        if (!res.ok) throw new Error(`Failed to fetch share config: ${res.status}`);
        const data = await res.json();

        __UMAMI_INTERNAL = {
            baseUrl: apiBase,
            websiteId: data.websiteId,
            shareToken: data.token,
            shareId: data.shareId,
            isReady: true
        };

        const links = document.querySelectorAll('.umami-link');
        links.forEach(link => link.setAttribute('href', UMAMI_CONFIG.shareUrl));

    } catch (e) {
        console.error('Umami Config Init Failed:', e);
    }
}

function formatNumber(num: number): string {
    if (num >= 1000000) {
        return (num / 1000000).toFixed(1) + 'M';
    } else if (num >= 1000) {
        return (num / 1000).toFixed(1) + 'K';
    }
    return Math.round(num).toString();
}

function setStats(values: { pageviews: number; visits: number; visitors: number }) {
    const pageviewsElements = document.querySelectorAll('.umami-total-pageviews');
    const visitsElements = document.querySelectorAll('.umami-total-visits');
    const visitorsElements = document.querySelectorAll('.umami-total-visitors');

    const easeOutCubic = (t: number) => 1 - Math.pow(1 - t, 3);
    const animHandles = new Map<HTMLElement, number>();

    const animateStat = (el: HTMLElement | null, to: number, duration = 2000) => {
        if (!el) return;

        const prev = animHandles.get(el);
        if (prev) cancelAnimationFrame(prev);

        const from = 0;
        const startTime = performance.now();

        const tick = (now: number) => {
            const elapsed = now - startTime;
            const progress = Math.min(1, elapsed / duration);
            const easedProgress = easeOutCubic(progress);

            const current = from + (to - from) * easedProgress;
            el.textContent = formatNumber(current);

            if (progress < 1) {
                animHandles.set(el, requestAnimationFrame(tick));
            }
        };
        animHandles.set(el, requestAnimationFrame(tick));
    };

    pageviewsElements.forEach(el => animateStat(el as HTMLElement, values.pageviews));
    visitsElements.forEach(el => animateStat(el as HTMLElement, values.visits));
    visitorsElements.forEach(el => animateStat(el as HTMLElement, values.visitors));
}

async function fetchUmamiStats() {
    if (!__UMAMI_INTERNAL.isReady) {
        await initUmamiConfig();
    }

    if (!__UMAMI_INTERNAL.isReady) {
        setStats(FALLBACK_STATS);
        return;
    }

    try {
        const endAt = Date.now();
        const startAt = 0;
        const url = `${__UMAMI_INTERNAL.baseUrl}/websites/${__UMAMI_INTERNAL.websiteId}/stats?startAt=${startAt}&endAt=${endAt}&unit=hour&timezone=Asia%2FShanghai`;

        const response = await fetch(url, {
            headers: {
                'x-umami-share-context': '1',
                'x-umami-share-token': __UMAMI_INTERNAL.shareToken
            }
        });

        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const data = await response.json();
        const getValue = (field: any) => (typeof field === 'object' ? field?.value : field) || 0;

        setStats({
            pageviews: getValue(data.pageviews),
            visits: getValue(data.visits),
            visitors: getValue(data.visitors),
        });

    } catch (error) {
        console.error('Umami Fetch Failed:', error);
        setStats(FALLBACK_STATS);
    }
}

let __umamiStatsStarted = false;
function startUmamiStats() {
    if (__umamiStatsStarted) return;
    __umamiStatsStarted = true;
    fetchUmamiStats();
}

function initUmamiStatsVisibility() {
    const containers = document.querySelectorAll('.umami-stats-container');
    const io = new IntersectionObserver((entries) => {
        let isAnyVisible = false;
        entries.forEach(entry => {
            if (entry.isIntersecting) isAnyVisible = true;
        });

        if (isAnyVisible) {
            startUmamiStats();
            io.disconnect();
        }
    }, { threshold: 0.1 });

    containers.forEach(container => io.observe(container));
}

initUmamiStatsVisibility();

if (window.swup) {
    window.swup.hooks.on('page:view', () => {
        __umamiStatsStarted = false;
        initUmamiStatsVisibility();
    });
}
</script>
```

其中的 `shareUrl` 部分，改成你的分享链接。

```astro {2}
const UMAMI_CONFIG = {
    shareUrl: 'https://xxx.xxx.xxx/share/xxx',
};
```

# 导入组件

文件路径：`src/components/layout/SideBar.astro`

```astro {2,7}
---
import UmamiStats from "@/components/widget/UmamiStats.astro";
---

// 组件映射表
const componentMap = {
  umamiStats: UmamiStats,
} satisfies Record<WidgetComponentType, typeof Profile>;
```

# 配置侧边栏

文件路径：`src/config/sidebarConfig.ts`

```ts 
{
    // 组件类型：Umami 统计组件
    type: "umamiStats",
    // 是否启用该组件
    enable: true,
    // 组件位置
    position: "top",
    // 是否在文章详情页显示
    showOnPostPage: true,
},
```

# 配置脚本

文件路径：`src/config/analyticsConfig.ts`

```astro {4,6,8}
	// Umami 统计配置
	umamiAnalytics: {
		// Umami Website ID
		websiteId: "网站ID",
		// Umami JS地址，支持使用自建
		scriptUrl: "https://自建地址/script.js",
		// Umami 会话回放脚本地址，支持使用自建
		replaysScriptUrl: "https://自建地址/recorder.js",
```

# 自定义统计时间

默认拉取所有时间，如果需要调整，修改 `fetchUmamiStats` 函数内 `startAt` 参数。

常用时间周期公式（毫秒）：86400000 = 24 小时、2592000000 = 最近 30 天、7776000000 = 最近 90 天。

例如改成最近 30 天：

```astro {3,4}
try {
    const endAt = Date.now();
    const startAt = 0;
    const startAt = Date.now() - 2592000000;
```

# 调整备用数据

当接口请求失败，会显示备用数据，如果需要调整，修改 `FALLBACK_STATS` 函数内参数。

```astro {2,3,4}
const FALLBACK_STATS = {
    pageviews: 1000, // 备用总浏览量
    visits: 1000,    // 备用访问数
    visitors: 1000,  // 备用游客数
};
```

# 结尾

完成以上步骤，已成功在 Firefly 主题侧边栏添加一个交互感丰富、自动解析、支持查看详情的 Umami 统计卡片。