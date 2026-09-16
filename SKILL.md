---
name: maml-backscreen-theme
description: 小米手机背屏(后盖屏) MAML 主题开发技能。包含 WebView 加载 HTML 到背屏、摄像头避让规则、MAML 系统变量传递、HTML 唤起安卓应用(doAction 机制)、AOD 息屏 HTML 侧实现、XP 桌面交互等全套实战经验。用于创建/修改/排障小米背屏主题(.mrc/.mtz/.zip)。
author: 唯梦倾城
version: 2.8
license: MIT
tags: [miui, maml, backscreen, theme, webview, xiaomi, hyperos]
---

# MAML 背屏主题开发技能

小米背屏(后盖屏/小屏)主题开发完整技能包，基于多主题实战 + 反编译破解验证。

## 何时使用

- 用户要求创建/修改小米背屏主题（开机动画、桌面、时钟、充电动画等）
- 需要把 HTML/WebView 内容加载到背屏
- 需要读取系统数据（存储/电量/步数/开机时长）显示在背屏
- 需要在背屏点击图标唤起安卓应用
- 需要 AOD 息屏显示、摄像头避让、多机型适配

## 核心能力

1. **WebView 加载 HTML 到背屏**：manifest.xml 用 `<WebView>` 元素，本地 HTML 放 web/ 目录
2. **摄像头避让**：左侧 277px 摄像头区，内容用 `left:29vw` 安全区，背景可全屏
3. **MAML 系统变量传递**：ContentProviderBinder 读存储/步数，`window.maml.getDoubleByName` 在 HTML 里读取
4. **HTML 唤起安卓应用**：`window.maml.doAction('xxx')` 触发 WebView 内 `<Triggers><Trigger action="xxx">` 的 IntentCommand（反编译破解）
5. **AOD 息屏 HTML 侧实现**：MAML `enterAod → RUNJS __setAod(1)` 通知 HTML 切 AOD 画面（不能用 MAML `<Aod>` 元素）
6. **XP 桌面交互**：窗口拖动、显示桌面、托盘点击、开始菜单等

## 目录结构

```
skill-docs/                    # 详细技能文档（19 篇）
├── 00-搜索索引.md             # AI 优先读取：关键词→文件映射
├── 基础语法/                  # 变量、UI、命令、动画
├── 实战技巧/                  # 机型适配、AOD、WebView、踩坑
├── 进阶主题/                  # GL、摄像头、日程、电池
└── 配置规范/                  # var_config、description.xml
```

## 快速开始（AI 工具用）

1. 先读 `skill-docs/00-搜索索引.md` 定位关键词
2. 新建主题：读 `实战技巧/14-背屏Web主题-AI创作规范.md` + `15-精华速览与黄金标准.md`
3. WebView 主题：读 `实战技巧/12-WebView加载HTML到背屏.md`
4. 完整示例：读 `实战技巧/13-HTML背屏主题完整示例-星舰矩阵时钟.md`
5. 安装测试：`miroot_theme_test_install` **优先 Hook 直接安装（directApply=true，仅 root+模块生效，失败自动回退替换流程）**；替换流程为替换 AI 文件夹主题 + 跳转主题设置手动应用，不重启背屏

## 关键速查

### 摄像头避让（absolute 布局）
```css
.safe{position:absolute;left:29vw;top:0;right:0;bottom:0}  /* 内容安全区 */
.wp{position:fixed;left:0;top:0;width:100vw;height:100vh}  /* 背景全屏 */
```

### HTML 唤起应用（doAction 机制）
```xml
<WebView name="wv" ...>
  <Triggers>
    <Trigger action="launch_app">
      <IntentCommand action="android.intent.action.MAIN" package="xx.xx" class="xx.xx.Activity"/>
    </Trigger>
  </Triggers>
</WebView>
```
```js
window.maml.doAction('launch_app');  // ⚠️ Trigger 必须在 WebView 元素内，不是 ExternalCommands！
```

### AOD 息屏（HTML 侧）
```xml
<Trigger action="enterAod"><WebViewCommand target="wv" command="runjs" params="'__setAod(1)'"/></Trigger>
<Trigger action="exitAod"><WebViewCommand target="wv" command="runjs" params="'__setAod(0)'"/></Trigger>
```
```js
window.__setAod = function(v){ v===1 ? $('#aod').classList.add('show') : $('#aod').classList.remove('show'); };
```

### MAML 系统变量（存储/步数/开机）
```xml
<ContentProviderBinder name="getStorageData" uri="content://com.miui.securitycenter.widgetProvider/getCleanMasterData" columns="availableSpace,totalSpace" countName="hasStorageData">
  <Variable name="_availableSpace" type="long" column="availableSpace"/>
</ContentProviderBinder>
```
```js
var availGB = window.maml.getDoubleByName('_availableSpace')/(1024*1024*1024);
```

## 注意

- 背屏视口 348×212（dpr 2.8125），不是 976×596；避让用 vw 不用 px
- 双击会息屏：桌面图标必须单击即开
- 右下 35dp 圆角：托盘/时间要留 padding 避让
- **打包统一 .zip 后缀**：`miroot_theme_pack` format=zip / outputName 写完整 `主题名_vX.Y.zip`，**严禁 .zip.zip / .mrc.mrc 重复后缀**；测试用 `miroot_theme_test_install`（**优先 Hook directApply=true，失败回退替换**）
