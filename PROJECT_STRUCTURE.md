# Kiss Translator 项目结构

一个极简双语翻译浏览器扩展 & 油猴脚本，支持 Chrome / Edge / Firefox / Safari / Thunderbird 及 Web 版。

---

## 入口文件

| 文件 | 用途 |
|------|------|
| [src/background.js](src/background.js) | Service Worker（扩展后台），处理消息路由、快捷键命令、右键菜单、CSP 注入、图标状态等 |
| [src/content.js](src/content.js) | Content Script 入口，设置上下文标识后调用 `common.js` 的 `run()` |
| [src/common.js](src/common.js) | 核心启动逻辑，判断运行环境（扩展/油猴/Web），初始化 `TranslatorManager` |
| [src/userscript.js](src/userscript.js) | 油猴脚本专用入口 |
| [src/options.js](src/options.js) | 扩展选项页入口（React 挂载点） |
| [src/popup.js](src/popup.js) | 扩展弹出窗口入口（React 挂载点） |
| [src/rules.js](src/rules.js) | 构建时脚本，生成内置规则文件 |
| [src/injector-shadowroot.js](src/injector-shadowroot.js) | 注入到页面的 Shadow DOM 处理脚本 |
| [src/injector-subtitle.js](src/injector-subtitle.js) | 注入到页面的字幕拦截脚本 |

---

## src/libs — 核心功能库

### 翻译主流程

| 文件 | 用途 |
|------|------|
| [src/libs/translatorManager.js](src/libs/translatorManager.js) | 顶层管理器，协调 `Translator`、`TransboxManager`、`PopupManager`、`FabManager`、快捷键、触摸事件 |
| [src/libs/translator.js](src/libs/translator.js) | 核心翻译引擎，负责 DOM 扫描、文本提取、翻译结果渲染、样式切换 |
| [src/libs/batchQueue.js](src/libs/batchQueue.js) | 批处理队列，将翻译请求按批次/间隔聚合后发送，支持异步生成器逐条返回结果 |
| [src/libs/pool.js](src/libs/pool.js) | 任务池，控制并发数量和请求间隔，带自动重试 |
| [src/libs/stream.js](src/libs/stream.js) | SSE 流解析器 + JSON 流解析器，处理 AI 接口的流式响应 |
| [src/libs/detect.js](src/libs/detect.js) | 语言检测，调用 API 或浏览器内置能力 |
| [src/libs/builtinAI.js](src/libs/builtinAI.js) | Chrome 浏览器内置 AI（`LanguageDetector` / `Translator`）封装 |
| [src/libs/interpreter.js](src/libs/interpreter.js) | 基于 Sval 的沙箱 JS 解释器，用于执行用户自定义翻译规则脚本 |

### UI 组件管理

| 文件 | 用途 |
|------|------|
| [src/libs/shadowDomManager.js](src/libs/shadowDomManager.js) | Shadow DOM 管理基类，将 React 组件挂载到隔离的 Shadow Root |
| [src/libs/domManager.js](src/libs/domManager.js) | 普通 DOM 管理器，将 React 组件挂载到普通 DOM 节点 |
| [src/libs/tranbox.js](src/libs/tranbox.js) | 划词翻译框（TranBox）管理，继承 Shadow DOM 挂载 |
| [src/libs/popupManager.js](src/libs/popupManager.js) | 页面内弹出操作面板管理（继承 `ShadowDomManager`） |
| [src/libs/fabManager.js](src/libs/fabManager.js) | 悬浮按钮（FAB）管理（继承 `ShadowDomManager`） |

### 规则与配置

| 文件 | 用途 |
|------|------|
| [src/libs/rules.js](src/libs/rules.js) | 规则匹配与合并逻辑，支持 `+/-` 补丁模式的 CSS 选择器合并 |
| [src/libs/subRules.js](src/libs/subRules.js) | 订阅规则加载与同步 |
| [src/libs/blacklist.js](src/libs/blacklist.js) | 黑名单判断（域名/URL 过滤） |
| [src/libs/style.js](src/libs/style.js) | 翻译样式类名生成（双语/仅译文/高亮等） |

### 存储与同步

| 文件 | 用途 |
|------|------|
| [src/libs/storage.js](src/libs/storage.js) | 本地存储读写封装（`browser.storage` / `GM_getValue`） |
| [src/libs/sync.js](src/libs/sync.js) | 设置/规则/词汇的云同步，支持 WebDAV 和自定义 KV 服务 |
| [src/libs/cache.js](src/libs/cache.js) | HTTP 缓存 polyfill，用于缓存翻译结果 |

### 网络请求

| 文件 | 用途 |
|------|------|
| [src/libs/fetch.js](src/libs/fetch.js) | fetch 封装，处理扩展/油猴环境差异，支持 GM_xmlhttpRequest |
| [src/libs/auth.js](src/libs/auth.js) | 请求鉴权处理 |

### 注入与环境

| 文件 | 用途 |
|------|------|
| [src/libs/injector.js](src/libs/injector.js) | 向页面注入外部 JS / 内联 JS / CSS |
| [src/libs/browser.js](src/libs/browser.js) | 浏览器 API 兼容封装（`webextension-polyfill` 包装） |
| [src/libs/client.js](src/libs/client.js) | 判断当前运行客户端类型（扩展/油猴/Web） |
| [src/libs/gm.js](src/libs/gm.js) | 油猴专用 API 封装（`GM_*` 函数） |
| [src/libs/iframe.js](src/libs/iframe.js) | iframe 环境检测与跨 frame 消息通信 |
| [src/libs/msg.js](src/libs/msg.js) | 扩展消息收发封装（background ↔ content） |
| [src/libs/trustedTypes.js](src/libs/trustedTypes.js) | Trusted Types API 兼容处理，防止 XSS |

### 交互功能

| 文件 | 用途 |
|------|------|
| [src/libs/inputTranslate.js](src/libs/inputTranslate.js) | 输入框翻译功能，监听输入框快捷键触发翻译 |
| [src/libs/shortcut.js](src/libs/shortcut.js) | 快捷键注册与管理 |
| [src/libs/touch.js](src/libs/touch.js) | 移动端触摸事件处理 |
| [src/libs/mobile.js](src/libs/mobile.js) | 移动端环境检测 |
| [src/libs/docInfo.js](src/libs/docInfo.js) | 获取当前页面文档信息（标题、URL 等） |

### 工具

| 文件 | 用途 |
|------|------|
| [src/libs/utils.js](src/libs/utils.js) | 通用工具函数（防抖、哈希、字符串处理等） |
| [src/libs/log.js](src/libs/log.js) | 日志工具，支持日志级别控制 |
| [src/libs/svg.js](src/libs/svg.js) | 内联 SVG 图标生成（加载中、重试等） |
| [src/libs/url.js](src/libs/url.js) | URL 处理工具 |

---

## src/apis — 翻译 API 接入层

| 文件 | 用途 |
|------|------|
| [src/apis/index.js](src/apis/index.js) | API 统一入口，路由到各翻译服务，处理缓存、批处理、语言检测 |
| [src/apis/trans.js](src/apis/trans.js) | 各翻译服务的具体请求实现（Google、Microsoft、DeepL、OpenAI、Gemini、Claude、百度、腾讯等 20+ 服务） |
| [src/apis/baidu.js](src/apis/baidu.js) | 百度翻译 API 签名生成 |
| [src/apis/deepl.js](src/apis/deepl.js) | DeepL API 封装 |
| [src/apis/history.js](src/apis/history.js) | 翻译历史记录管理 |
| [src/apis/zdic.js](src/apis/zdic.js) | 汉典词典查询 |

---

## src/config — 常量与默认配置

| 文件 | 用途 |
|------|------|
| [src/config/index.js](src/config/index.js) | 统一导出所有配置模块 |
| [src/config/app.js](src/config/app.js) | 应用级常量（名称、版本、DOM ID 等） |
| [src/config/api.js](src/config/api.js) | 翻译 API 类型枚举、默认 API 配置、Prompt 模板 |
| [src/config/setting.js](src/config/setting.js) | 用户设置默认值（翻译样式、语言、字幕等） |
| [src/config/rules.js](src/config/rules.js) | 内置翻译规则默认值 |
| [src/config/msg.js](src/config/msg.js) | 消息类型常量（`MSG_*`、`CMD_*`、`EVENT_*`） |
| [src/config/storage.js](src/config/storage.js) | 存储键名常量（`KV_*`） |
| [src/config/url.js](src/config/url.js) | 内部 URL 常量 |
| [src/config/i18n.js](src/config/i18n.js) | 国际化工具函数 |
| [src/config/client.js](src/config/client.js) | 客户端类型常量 |
| [src/config/styles.js](src/config/styles.js) | 翻译样式选项枚举 |
| [src/config/quotes.js](src/config/quotes.js) | 引号配置 |

---

## src/hooks — React 自定义 Hooks

| 文件 | 用途 |
|------|------|
| [src/hooks/Setting.js](src/hooks/Setting.js) | 读写用户设置 |
| [src/hooks/Rules.js](src/hooks/Rules.js) | 翻译规则状态管理 |
| [src/hooks/Api.js](src/hooks/Api.js) | API 配置状态管理 |
| [src/hooks/Storage.js](src/hooks/Storage.js) | 通用存储 hook |
| [src/hooks/Sync.js](src/hooks/Sync.js) | 云同步操作 hook |
| [src/hooks/Fetch.js](src/hooks/Fetch.js) | 数据请求 hook |
| [src/hooks/I18n.js](src/hooks/I18n.js) | 国际化 hook |
| [src/hooks/Theme.js](src/hooks/Theme.js) | MUI 主题 hook |
| [src/hooks/ColorMode.js](src/hooks/ColorMode.js) | 深色/浅色模式切换 |
| [src/hooks/CustomStyles.js](src/hooks/CustomStyles.js) | 自定义样式注入 hook |
| [src/hooks/Shortcut.js](src/hooks/Shortcut.js) | 快捷键配置 hook |
| [src/hooks/Subtitle.js](src/hooks/Subtitle.js) | 字幕设置 hook |
| [src/hooks/Tranbox.js](src/hooks/Tranbox.js) | 划词翻译框设置 hook |
| [src/hooks/SubRules.js](src/hooks/SubRules.js) | 订阅规则 hook |
| [src/hooks/FavWords.js](src/hooks/FavWords.js) | 收藏词汇 hook |
| [src/hooks/InputRule.js](src/hooks/InputRule.js) | 输入框翻译规则 hook |
| [src/hooks/MouseHover.js](src/hooks/MouseHover.js) | 鼠标悬停翻译设置 hook |
| [src/hooks/Fab.js](src/hooks/Fab.js) | 悬浮按钮配置 hook |
| [src/hooks/Alert.js](src/hooks/Alert.js) | 全局提示 hook |
| [src/hooks/Confirm.js](src/hooks/Confirm.js) | 确认对话框 hook |
| [src/hooks/Loading.js](src/hooks/Loading.js) | 加载状态 hook |
| [src/hooks/Audio.js](src/hooks/Audio.js) | 音频播放 hook（TTS） |
| [src/hooks/DebouncedCallback.js](src/hooks/DebouncedCallback.js) | 防抖回调 hook |
| [src/hooks/WindowSize.js](src/hooks/WindowSize.js) | 窗口尺寸监听 hook |
| [src/hooks/ValidationInput.js](src/hooks/ValidationInput.js) | 带校验的输入框 hook |
| [src/hooks/useAutoHideTranBtn.js](src/hooks/useAutoHideTranBtn.js) | 翻译按钮自动隐藏逻辑 |
| [src/hooks/useSelectionController.js](src/hooks/useSelectionController.js) | 文本选中状态控制 |
| [src/hooks/useTranboxShortcuts.js](src/hooks/useTranboxShortcuts.js) | 翻译框快捷键 |
| [src/hooks/useTranBoxState.js](src/hooks/useTranBoxState.js) | 翻译框状态管理 |

---

## src/views — React UI 组件

### Selection（划词翻译框）

| 文件 | 用途 |
|------|------|
| [src/views/Selection/index.js](src/views/Selection/index.js) | 划词翻译框根组件 |
| [src/views/Selection/TranBox.js](src/views/Selection/TranBox.js) | 翻译框容器 |
| [src/views/Selection/TranBtn.js](src/views/Selection/TranBtn.js) | 翻译触发按钮 |
| [src/views/Selection/TranCont.js](src/views/Selection/TranCont.js) | 翻译结果展示区 |
| [src/views/Selection/TranForm.js](src/views/Selection/TranForm.js) | 翻译输入表单 |
| [src/views/Selection/DictCont.js](src/views/Selection/DictCont.js) | 词典结果展示 |
| [src/views/Selection/DictHandler.js](src/views/Selection/DictHandler.js) | 词典数据处理 |
| [src/views/Selection/SugCont.js](src/views/Selection/SugCont.js) | 翻译建议/候选展示 |
| [src/views/Selection/AudioBtn.js](src/views/Selection/AudioBtn.js) | 朗读按钮 |
| [src/views/Selection/CopyBtn.js](src/views/Selection/CopyBtn.js) | 复制按钮 |
| [src/views/Selection/FavBtn.js](src/views/Selection/FavBtn.js) | 收藏按钮 |
| [src/views/Selection/Zdic.js](src/views/Selection/Zdic.js) | 汉典词典展示 |
| [src/views/Selection/DraggableResizable.js](src/views/Selection/DraggableResizable.js) | 可拖拽可缩放容器 |

### Action（页面内弹出面板）

| 文件 | 用途 |
|------|------|
| [src/views/Action/index.js](src/views/Action/index.js) | 操作面板根组件（翻译开关、样式切换等） |
| [src/views/Action/ContentFab.js](src/views/Action/ContentFab.js) | 页面悬浮按钮（FAB）组件 |
| [src/views/Action/Draggable.js](src/views/Action/Draggable.js) | 可拖拽容器 |

### Popup（扩展弹出窗口）

| 文件 | 用途 |
|------|------|
| [src/views/Popup/index.js](src/views/Popup/index.js) | 弹出窗口根组件 |
| [src/views/Popup/Header.js](src/views/Popup/Header.js) | 弹出窗口顶栏 |
| [src/views/Popup/PopupCont.js](src/views/Popup/PopupCont.js) | 弹出窗口内容区 |

### Options（选项设置页）

| 文件 | 用途 |
|------|------|
| [src/views/Options/index.js](src/views/Options/index.js) | 选项页根组件 |
| [src/views/Options/Layout.js](src/views/Options/Layout.js) | 选项页布局 |
| [src/views/Options/Navigator.js](src/views/Options/Navigator.js) | 侧边导航栏 |
| [src/views/Options/Header.js](src/views/Options/Header.js) | 顶部标题栏 |
| [src/views/Options/Setting.js](src/views/Options/Setting.js) | 通用设置面板 |
| [src/views/Options/Apis.js](src/views/Options/Apis.js) | API 配置面板 |
| [src/views/Options/Rules.js](src/views/Options/Rules.js) | 翻译规则配置面板 |
| [src/views/Options/Subtitle.js](src/views/Options/Subtitle.js) | 字幕设置面板 |
| [src/views/Options/Tranbox.js](src/views/Options/Tranbox.js) | 划词翻译框设置面板 |
| [src/views/Options/MouseHover.js](src/views/Options/MouseHover.js) | 鼠标悬停翻译设置面板 |
| [src/views/Options/InputSetting.js](src/views/Options/InputSetting.js) | 输入框翻译设置面板 |
| [src/views/Options/StylesSetting.js](src/views/Options/StylesSetting.js) | 翻译样式设置面板 |
| [src/views/Options/SyncSetting.js](src/views/Options/SyncSetting.js) | 云同步设置面板 |
| [src/views/Options/FavWords.js](src/views/Options/FavWords.js) | 收藏词汇管理面板 |
| [src/views/Options/Playground.js](src/views/Options/Playground.js) | API 测试沙盒 |
| [src/views/Options/About.js](src/views/Options/About.js) | 关于页面 |
| [src/views/Options/ShortcutInput.js](src/views/Options/ShortcutInput.js) | 快捷键输入组件 |
| [src/views/Options/ReusableAutocomplete.js](src/views/Options/ReusableAutocomplete.js) | 可复用自动补全输入框 |
| [src/views/Options/DarkModeButton.js](src/views/Options/DarkModeButton.js) | 深色模式切换按钮 |
| [src/views/Options/DownloadButton.js](src/views/Options/DownloadButton.js) | 配置导出按钮 |
| [src/views/Options/UploadButton.js](src/views/Options/UploadButton.js) | 配置导入按钮 |
| [src/views/Options/HelpButton.js](src/views/Options/HelpButton.js) | 帮助按钮 |
| [src/views/Options/ShowMoreButton.js](src/views/Options/ShowMoreButton.js) | 展开更多按钮 |

---

## src/subtitle — 字幕翻译

| 文件 | 用途 |
|------|------|
| [src/subtitle/subtitle.js](src/subtitle/subtitle.js) | 字幕功能入口，匹配页面 URL 选择对应 Provider |
| [src/subtitle/YouTubeCaptionProvider.js](src/subtitle/YouTubeCaptionProvider.js) | YouTube 字幕数据获取（拦截 XHR） |
| [src/subtitle/YouTubeSubtitleList.js](src/subtitle/YouTubeSubtitleList.js) | YouTube 字幕列表管理 |
| [src/subtitle/BilingualSubtitleManager.js](src/subtitle/BilingualSubtitleManager.js) | 双语字幕渲染管理 |
| [src/subtitle/Menus.js](src/subtitle/Menus.js) | 字幕控制菜单 |
| [src/subtitle/modes.js](src/subtitle/modes.js) | 字幕显示模式定义 |
| [src/subtitle/sentenceBreaker.js](src/subtitle/sentenceBreaker.js) | 字幕句子分割处理 |
| [src/subtitle/vtt.js](src/subtitle/vtt.js) | VTT 字幕格式解析 |

---

## src/injectors — 页面注入器

| 文件 | 用途 |
|------|------|
| [src/injectors/index.js](src/injectors/index.js) | 注入器注册表，映射注入器名称到实现 |
| [src/injectors/shadowroot.js](src/injectors/shadowroot.js) | Shadow DOM 注入器（处理 Shadow Root 内的翻译） |
| [src/injectors/xmlhttp.js](src/injectors/xmlhttp.js) | XMLHttpRequest 拦截器（用于字幕数据捕获） |

---

## src/components — 共享 UI 组件

| 文件 | 用途 |
|------|------|
| [src/components/Logo/index.js](src/components/Logo/index.js) | Logo 组件 |
| [src/components/Logo/icon.base64.js](src/components/Logo/icon.base64.js) | Logo Base64 图片数据 |

---

## src/scripts — 构建脚本

| 文件 | 用途 |
|------|------|
| [src/scripts/build-task.mjs](src/scripts/build-task.mjs) | 多目标构建任务（chrome/edge/firefox/safari/thunderbird/web） |
| [src/scripts/archive.mjs](src/scripts/archive.mjs) | 打包压缩构建产物 |
| [src/scripts/sync-version.mjs](src/scripts/sync-version.mjs) | 同步版本号到各配置文件 |
| [src/scripts/update-version.mjs](src/scripts/update-version.mjs) | 更新版本号（patch/minor/major） |
| [src/scripts/build-safari.mjs](src/scripts/build-safari.mjs) | Safari 扩展构建 |
| [src/scripts/build-ios.mjs](src/scripts/build-ios.mjs) | iOS 油猴脚本构建 |

---

## public — 静态资源

| 文件 | 用途 |
|------|------|
| [public/manifest.json](public/manifest.json) | Chrome/Edge 扩展 Manifest V3 |
| [public/manifest.firefox.json](public/manifest.firefox.json) | Firefox 扩展 Manifest |
| [public/manifest.thunderbird.json](public/manifest.thunderbird.json) | Thunderbird 扩展 Manifest |
| [public/index.html](public/index.html) | Web 版 / 选项页 HTML 模板 |
| [public/content.html](public/content.html) | Content Script HTML 模板 |
| [public/_locales/](public/_locales/) | 国际化语言包（zh_CN / zh_TW / en / ja / ko / de / es / fr） |
| [public/images/](public/images/) | 扩展图标（16/32/48/128/192px，含激活态） |

---

## 根目录配置文件

| 文件 | 用途 |
|------|------|
| [package.json](package.json) | 项目依赖与 npm 脚本 |
| [config-overrides.js](config-overrides.js) | `react-app-rewired` 构建配置覆盖 |
| [.babelrc](.babelrc) | Babel 配置 |
| [.env](.env) | 环境变量（构建时注入） |
| [.prettierrc](.prettierrc) | Prettier 代码格式化配置 |
| [custom-api.md](custom-api.md) | 自定义 API 接入文档（v1） |
| [custom-api_v2.md](custom-api_v2.md) | 自定义 API 接入文档（v2） |
| [CHANGELOG.md](CHANGELOG.md) | 版本更新日志 |

---

## 架构概览

```
background.js          ← Service Worker，消息总线
    ↕ 消息通信
content.js → common.js → TranslatorManager
                              ├── Translator          ← DOM 扫描 + 翻译渲染
                              ├── TransboxManager     ← 划词翻译框
                              ├── PopupManager        ← 页面内操作面板
                              ├── FabManager          ← 悬浮按钮
                              └── InputTranslator     ← 输入框翻译

Translator → apis/index.js → apis/trans.js → 各翻译服务
           → libs/batchQueue.js + libs/pool.js  ← 并发控制
           → libs/stream.js                     ← 流式响应
```
