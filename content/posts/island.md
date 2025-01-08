---
title: "Blowfish主题：更cool的悬浮导航栏"
date: 2024-11-19T21:19:35+08:00
draft: false
tags: ["hugo","Blowfish","网页设计"]
---
## 缘起

为什么要去自定义一个头部导航栏？其实`blowfish`本身已经提供了很多的样式，比如最基本的`basic`，还有各种的`fixed`。只论实用性的话，或许`blowfish`自带的这些已经很好了，看起来简约大方。但是根据自己的想法去diy一个看起来cool的设计，可能是不少折腾网站的人都会踩过的坑吧。

设计这个菜单栏的想法来自[加绒](https://www.jrhim.com/)的[Hugo Blowfish 自定义：导航栏上磨砂岛](https://www.jrhim.com/posts/series-decorating/island-header/)，可能是看惯了自己原本的fixed导航栏，第一次看到这种居中悬浮的设计觉得挺有意思。这位博主在文中描述了最重要的几个部分，并将源代码放在文末。对于我这种见好就收的白嫖党，第一想法肯定是二话不说咣咣复制。但是，遗憾的是，文末的html文件似乎失效了，于是乎只能摸着石头过河，从蛛丝马迹中尝试复现。

## 设计想法

菜单栏的设计包括以下几个方面：

1. 悬浮居中，边框圆角
2. 长度适中，蓝绿渐变
3. 下划隐藏，上划显示
4. 顶部透明，逐渐模糊

对于我这种只学过一点点编程，而且从没学过前端的新手来说，单独靠自己是完全不可能实现这些想法的。于是，我尝试把想法转译成具体的命令，借助AI帮我完成。恰好github有教育版免费的Copilot（非常nice），可以使用的模型有Clade 3.5 Sonnet、GPT 4和o1 mini等。

接下来就是各种各样的试错环节，报错是千奇百怪的，比如导航栏消失了，按钮点不动，颜色使用一直不对等情况，反正挺考验人的心态。最后我给出的命令是让ai参照原本的basic.html去写，非必要不更改。在几个大模型中，只有Clade给出了正确答案。


## 实现

首先需要新建`/layouts/partials/header/float.html`文件，然后在`params.toml`中将header选项下的layout设置成float。接下来将这段代码复制到`float.html`中：

{{< collapse title="float.html" >}}
```html
<!-- 更新滚动控制脚本 -->
<!-- 更新滚动控制脚本 -->
<script>
let lastScrollTop = 0;
let lastScrollDirection = 0;
let island_header_style_top = 15;
let accumulated_scroll = 0;

window.addEventListener("scroll", function () {
    const scroll = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0;
    let currScrollDirection = scroll - lastScrollTop > 0 ? 1 : -1;
    
    const island_header = document.getElementById("island-header");
    const subheader = document.querySelector('.main-menu + .main-menu');
    
    // 计算透明度和模糊度
    const opacity = Math.min(scroll / 300, 0.9);
    const blur = Math.min(scroll / 100 * 10, 10);
    
    // 应用背景和模糊效果
    island_header.style.backgroundColor = `rgba(255, 255, 255, ${opacity})`;
    island_header.style.backdropFilter = `blur(${blur}px)`;
    if(subheader) {
        subheader.style.backgroundColor = `rgba(255, 255, 255, ${opacity})`;
        subheader.style.backdropFilter = `blur(${blur}px)`;
    }
    
    // 处理滚动隐藏逻辑
    if (scroll > 100) {
        if (currScrollDirection !== lastScrollDirection) {
            accumulated_scroll = 0;
        } else {
            accumulated_scroll += Math.abs(scroll - lastScrollTop);
        }
        
        if (accumulated_scroll > 50 || currScrollDirection == -1) {
            // 计算新的top值
            island_header_style_top = currScrollDirection === 1 ? 
                Math.max(-100, island_header_style_top - 15) : // 向下滚动
                Math.min(15, island_header_style_top + 15);    // 向上滚动
                
            // 应用变换
            island_header.style.transform = `translateX(-50%) translateY(${island_header_style_top}px)`;
            if(subheader) {
                subheader.style.transform = `translateX(-50%) translateY(${island_header_style_top + 50}px)`;
            }
        }
    }
    
    lastScrollTop = scroll;
    lastScrollDirection = currScrollDirection;
});
</script>

<!-- 主要导航栏容器 -->
<div id="island-header" style="
    position: fixed;
    top: 15px;
    left: 50%;
    transform: translateX(-50%);
    width: 90%;  /* 移动端下宽度更大 */
    max-width: 900px;
    background: linear-gradient(
        135deg, 
        rgba(255, 255, 255, 0.45),
        rgba(147, 197, 253, 0.35) 20%,
        rgba(167, 243, 208, 0.4) 50%,
        rgba(147, 197, 253, 0.35) 80%,
        rgba(110, 231, 183, 0.4)
    );
    backdrop-filter: blur(8px);
    border: 1px solid rgba(255, 255, 255, 0.45);
    border-radius: 0.75rem;  /* 移动端圆角稍小 */
    box-shadow: 
        0 4px 6px rgba(0, 0, 0, 0.03),
        0 1px 3px rgba(147, 197, 253, 0.2) inset,
        0 2px 6px rgba(167, 243, 208, 0.15) inset;
    transition: all 0.3s ease;
    z-index: 1000;
    padding: 0.25rem;  /* 移动端内边距更小 */
    @media (min-width: 768px) {  /* 桌面端样式 */
        width: 70%;
        padding: 0.25rem 0.5rem;
        border-radius: 1rem;
    }
    "
    class="main-menu flex items-center justify-between px-2 py-1 sm:px-6 md:justify-start space-x-3">  
    
    {{ if .Site.Params.Logo }}
    {{ $logo := resources.Get .Site.Params.Logo }}
    {{ if $logo }}
    <div>
        <a href="{{ "" | relLangURL }}" class="flex">
            <span class="sr-only">{{ .Site.Title | markdownify }}</span>

            {{ if eq $logo.MediaType.SubType "svg" }}
            <span class="logo object-scale-down object-left nozoom">
                {{ $logo.Content | safeHTML }}
            </span>
            {{ else }}
            <img src="{{ $logo.RelPermalink }}" width="{{ div $logo.Width 2 }}" height="{{ div $logo.Height 2 }}"
                class="logo max-h-[5rem] max-w-[5rem] object-scale-down object-left nozoom" alt="{{ .Site.Title }}" />
            {{ end }}

        </a>
    </div>
    {{ end }}
    {{- end }}
    <div class="flex flex-1 items-center justify-between">
        <nav class="flex space-x-3">

            {{ if not .Site.Params.disableTextInHeader | default true }}
            <a href="{{ "" | relLangURL }}" class="text-base font-medium text-gray-500 hover:text-gray-900">{{
                .Site.Title | markdownify
                }}</a>
            {{ end }}

        </nav>
        <nav class="hidden md:flex items-center space-x-5 md:ml-12 h-12">

            {{ if .Site.Menus.main }}
            {{ range .Site.Menus.main }}
            {{ partial "header/header-option.html" . }}
            {{ end }}
            {{ end }}

            {{ partial "translations.html" . }}

            {{ if .Site.Params.enableSearch | default false }}
            <button id="search-button" aria-label="Search" class="text-base hover:text-primary-600 dark:hover:text-primary-400"
                title="{{ i18n " search.open_button_title" }}">
                {{ partial "icon.html" "search" }}
            </button>
            {{ end }}

            {{/* Appearance switch */}}
            {{ if .Site.Params.footer.showAppearanceSwitcher | default false }}
            <div
                class="{{ if .Site.Params.footer.showScrollToTop | default true -}} ltr:mr-14 rtl:ml-14 {{- end }} flex items-center">
                <button id="appearance-switcher" aria-label="Dark mode switcher" type="button" class="text-base hover:text-primary-600 dark:hover:text-primary-400">
                    <div class="flex items-center justify-center dark:hidden">
                        {{ partial "icon.html" "moon" }}
                    </div>
                    <div class="items-center justify-center hidden dark:flex">
                        {{ partial "icon.html" "sun" }}
                    </div>
                </button>
            </div>
            {{ end }}

        </nav>
        <div class="flex md:hidden items-center space-x-5 md:ml-12 h-12">

            <span></span>

            {{ partial "translations.html" . }}

            {{ if .Site.Params.enableSearch | default false }}
            <button id="search-button-mobile" aria-label="Search" class="text-base hover:text-primary-600 dark:hover:text-primary-400"
                title="{{ i18n " search.open_button_title" }}">
                {{ partial "icon.html" "search" }}
            </button>
            {{ end }}

            {{/* Appearance switch */}}
            {{ if .Site.Params.footer.showAppearanceSwitcher | default false }}
            <button id="appearance-switcher-mobile" aria-label="Dark mode switcher" type="button" class="text-base hover:text-primary-600 dark:hover:text-primary-400" style="margin-right:5px">
                <div class="flex items-center justify-center dark:hidden">
                    {{ partial "icon.html" "moon" }}
                </div>
                <div class="items-center justify-center hidden dark:flex">
                    {{ partial "icon.html" "sun" }}
                </div>
            </button>
            {{ end }}

        </div>
    </div>
    <div class="-my-2 -mr-2 md:hidden">

        <label id="menu-button" class="block">
            {{ if .Site.Menus.main }}
            <div class="cursor-pointer hover:text-primary-600 dark:hover:text-primary-400">
                {{ partial "icon.html" "bars" }}
            </div>
            <div id="menu-wrapper" style="padding-top:5px;"
                class="fixed inset-0 z-30 invisible w-screen h-screen m-0 overflow-auto transition-opacity opacity-0 cursor-default bg-neutral-100/50 backdrop-blur-sm dark:bg-neutral-900/50">
                <ul
                    class="flex space-y-2 mt-3 flex-col items-end w-full px-6 py-6 mx-auto overflow-visible list-none ltr:text-right rtl:text-left max-w-7xl">

                    <li id="menu-close-button">
                        <span
                            class="cursor-pointer inline-block align-text-bottom hover:text-primary-600 dark:hover:text-primary-400">
                            {{ partial "icon.html" "xmark" }}
                        </span>
                    </li>

                    {{ range .Site.Menus.main }}
                    {{ partial "header/header-mobile-option.html" . }}
                    {{ end }}

                </ul>
                {{ if .Site.Menus.subnavigation }}
                <hr>
                <ul
                    class="flex mt-4 flex-col items-end w-full px-6 py-6 mx-auto overflow-visible list-none ltr:text-right rtl:text-left max-w-7xl">
                    {{ range .Site.Menus.subnavigation }}
                    <li class="mb-1">
                        <a href="{{ .URL }}" {{ if or (strings.HasPrefix .URL "http:" ) (strings.HasPrefix .URL "https:" ) }} target="_blank" {{ end }} class="flex items-center">
                            {{ if .Pre }}
                            <span {{ if and .Pre .Name}} class="mr-3" {{ end }}>
                                {{ partial "icon.html" .Pre }}
                            </span>
                            {{ end }}
                            <p class="text-sm font-sm text-gray-500 hover:text-gray-900" title="{{ .Title }}">
                                {{ .Name | markdownify }}
                            </p>
                        </a>
                    </li>
                    {{ end }}
                </ul>
                {{ end }}
            </div>
            {{ end }}
        </label>
    </div>
</div>

{{ if .Site.Menus.subnavigation }}
<div class="main-menu flex pb-2 flex-col items-end justify-between md:justify-start space-x-3" 
    style="
    position: fixed;
    top: 65px;
    left: 50%;
    transform: translateX(-50%);
    width: 90%;  /* 移动端宽度更大 */
    max-width: 900px;
    background: linear-gradient(
        135deg, 
        rgba(255, 255, 255, 0.4),
        rgba(147, 197, 253, 0.3) 20%,
        rgba(167, 243, 208, 0.35) 50%,
        rgba(147, 197, 253, 0.3) 80%,
        rgba(110, 231, 183, 0.35)
    );
    backdrop-filter: blur(8px);
    border: 1px solid rgba(255, 255, 255, 0.45);
    border-radius: 0.75rem;  /* 移动端圆角稍小 */
    box-shadow: 
        0 4px 6px rgba(0, 0, 0, 0.03),
        0 1px 3px rgba(147, 197, 253, 0.2) inset,
        0 2px 6px rgba(167, 243, 208, 0.15) inset;
    z-index: 999;
    padding: 0.25rem;  /* 移动端内边距更小 */
    transition: all 0.3s ease;
    @media (min-width: 768px) {  /* 桌面端样式 */
        width: 70%;
        padding: 0.25rem 0.5rem;
        border-radius: 1rem;
    }
    ">
    <div class="hidden md:flex items-center space-x-5">
        {{ range .Site.Menus.subnavigation }}
        <a href="{{ .URL }}" {{ if or (strings.HasPrefix .URL "http:" ) (strings.HasPrefix .URL "https:" ) }}
            target="_blank" {{ end }} class="flex items-center">
            {{ if .Pre }}
            <span {{ if and .Pre .Name}} class="mr-1" {{ end }}>
                {{ partial "icon.html" .Pre }}
            </span>
            {{ end }}
            <p class="text-xs font-light text-gray-500 hover:text-gray-900" title="{{ .Title }}">
                {{ .Name | markdownify }}
            </p>
        </a>
        {{ end }}
    </div>
</div>
{{ end }}

{{ if .Site.Params.highlightCurrentMenuArea }}
<script>
    (function () {
        var $mainmenu = $('.main-menu');
        var path = window.location.pathname;
        $mainmenu.find('a[href="' + path + '"]').each(function (i, e) {
            $(e).children('p').addClass('active');
        });
    })();
</script>
{{ end }}

<!-- 更新主内容区域的上边距 -->
<style>
    main {
        margin-top: 60px !important;  /* 移动端上边距更小 */
    }
    .has-submenu main {
        margin-top: 100px !important;  /* 移动端上边距更小 */
    }
    @media (min-width: 768px) {
        main {
            margin-top: 80px !important;  /* 桌面端保持原有上边距 */
        }
        .has-submenu main {
            margin-top: 120px !important;  /* 桌面端保持原有上边距 */
        }
    }
</style>

<!-- 检测是否有子导航菜单并添加类名 -->
<script>
    if (document.querySelector('.main-menu + .main-menu')) {
        document.body.classList.add('has-submenu');
    }
</script>

```
{{< /collapse >}}

该菜单栏样式也能适配移动端（也是问的ai），有一部分注释，一些位置可以根据自己的需要调整。

现在大模型的能力确实发展到了一个比较高的水平。至少在代码方面，大模型能够理解页面代码的逻辑结构，同时如果有比较明确的提问的话，模型确实有一键直达的能力。目前大模型缺乏的主要是一些想象能力和创造能力，这意味着，如果你提出的需求太过空泛或者指意不明，你几乎不会获得一个满意的结果。比如你在描述时如果使用太多诸如精致，新颖，科技感等广泛的形容词，模型很大可能会给你加一些看起来有些简单的元素。正如一千个人的心中就有一千个哈姆雷特，模型并不知道这些形容词背后的你是怎么想的。而且天下没有免费的午餐，你不花点心思提问，大模型为什么要深入思考而不偷点懒呢，可能大模型本身就认为最简单的就是最好的，对节约算力和能源也是有好处的。