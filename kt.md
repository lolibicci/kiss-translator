这个浏览器翻译插件的代码组织得很清晰，下面按目录和核心文件拆解它们的作用。

---

### 📁 根目录配置与说明
| 文件 | 作用 |
|------|------|
| `.gitignore` | Git 忽略规则 |
| `requirements.txt` | 可能用于开发环境依赖或 Python 辅助工具 |
| `README.md` / `README.ja.md` / `README.ko.md` | 多语言说明文档（日、韩、中等） |

---

### 📁 顶层 JS 文件（核心模块）
| 文件 | 推测功能 |
|------|----------|
| `app.js` | 插件主逻辑入口或应用初始化 |
| `client.js` | 翻译客户端，对接翻译 API 的核心请求逻辑 |
| `index.js` | 统一导出/入口 |
| `messages.js` | 多语言文本消息管理 |
| `rules.js` | 用户自定义翻译规则/网站规则 |
| `settings.js` | 插件设置项的读写与默认值 |
| `storage.js` | 对浏览器存储（chrome.storage / localStorage）的封装 |
| `styles.js` | 注入页面的样式管理 |
| `url.js` | URL 匹配与解析工具 |

---

### 📁 `scripts/` 与 `config/`
- **scripts/**：可能存放构建脚本或辅助 Node 脚本。
- **config/**：插件配置文件（如 webpack、babel，或翻译服务接口地址等）。

---

### 📁 `hooks/` —— React Hooks（UI 状态与逻辑）
这些文件是 React 自定义 Hook，用于组合界面逻辑：

| 文件 | 作用 |
|------|------|
| `Alert.js` | 弹出提示 |
| `Api.js` | 管理 API 调用的状态 |
| `Audio.js` | 朗读/发音功能 |
| `ColorMode.js` | 暗色/亮色模式管理 |
| `Confirm.js` | 确认对话框 |
| `CustomStyles.js` | 用户自定义样式的注入 |
| `DebouncedCallback.js` | 防抖回调 |
| `Fab.js` | 悬浮按钮（FAB）逻辑 |
| `FavWords.js` | 收藏单词 |
| `Fetch.js` | 封装 fetch 请求 |
| `I18n.js` | 国际化 Hook |
| `InputRule.js` | 输入规则（如按回车翻译） |
| `Loading.js` | 加载状态 |
| `MouseHover.js` | 鼠标悬停翻译行为 |
| `Rules.js` | 规则管理（站点匹配等） |
| `Settings.js` | 设置界面状态 |
| `Shortcut.js` | 键盘快捷键 |
| `Storage.js` | 持久化存储的 Hook 封装 |
| `SubRules.js` | 子规则逻辑 |
| `Submit.js` | 表单提交或翻译提交 |
| `Sync.js` | 跨设备同步状态 |
| `Theme.js` | 主题管理 |
| `Tranbox.js` | 翻译弹窗/面板的主要状态 |
| `useAutoHideTranBtn.js` | 控制翻译按钮自动隐藏 |
| `useSelectionController.js` | 划词选中翻译的控制器 |
| `useTranBoxShortcuts.js` | 翻译面板内的快捷键 |
| `useTranBoxState.js` | 翻译面板的整体状态 |
| `ValidationInput.js` | 带校验的输入框 |
| `WindowSize.js` | 监听窗口尺寸变化 |

---

### 📁 `injectors/` —— 页面注入脚本
| 文件 | 作用 |
|------|------|
| `index.js` | 注入入口 |
| `shadowroot.js` | 使用 Shadow DOM 隔离注入内容的样式 |
| `xmlhttp.js` | 可能用于拦截页面 XHR 请求以实现某些翻译功能 |

---

### 📁 `libs/` —— 底层功能库
| 文件 | 作用 |
|------|------|
| `auth.js` | 用户认证（如登录第三方翻译服务） |
| `batchQueue.js` | 批量翻译请求队列，控制并发 |
| `blacklist.js` | 黑名单网站管理 |
| `browser.js` | 浏览器 API 适配（Chrome/Firefox/Edge） |
| `builtinAljs.js` | 内置的某种算法/解析工具 |
| `cache.js` | 翻译缓存管理 |
| `client.js` | 翻译客户端核心（构建请求、解析响应） |
| `detect.js` | 语言检测 |
| `docUrl.js` | 获取文档/当前页面 URL 的工具 |
| `domManager.js` | DOM 操作接管（注入、删除等） |
| `fabManager.js` | 悬浮按钮的管理器 |
| `fetch.js` | 封装网络请求（绕过 CORS 等） |
| `gm.js` | Greasemonkey / Tampermonkey 兼容层 |
| `iframe.js` | iframe 内的翻译处理 |
| `injector.js` | 注入脚本的调度器 |
| `inputTranslate.js` | 输入框翻译功能 |
| `interpreter.js` | 可能用于执行用户规则脚本 |
| `log.js` | 日志工具 |
| `mobile.js` | 移动端适配 |
| `msg.js` | 多端消息通信（background ↔ content ↔ popup） |
| `pool.js` | 对象池或连接池 |
| `popupManager.js` | 弹出面板管理器 |
| `rules.js` | 规则引擎核心 |
| `shortcut.js` | 快捷键注册与管理 |
| `storage.js` | 存储统一接口（底层） |
| `stream.js` | 流式翻译（如 ChatGPT 流式响应） |
| `style.js` | 样式操作工具 |
| `subRules.js` | 子规则处理 |
| `svg.js` | SVG 图标处理 |
| `sync.js` | 同步逻辑（跨设备） |
| `touch.js` | 触屏设备手势处理 |
| `tranbox.js` | 翻译弹出框的内容渲染逻辑 |
| `translator.js` | 翻译器抽象（调用不同翻译源） |
| `translatorManager.js` | 管理多个翻译引擎，切换、优先级等 |
| `trustedTypes.js` | 处理 Trusted Types 安全策略 |
| `url.js` | URL 工具函数 |
| `utils.js` | 通用工具函数集 |

---

### 📁 `actions/` —— 页面内可操作组件
| 文件 | 作用 |
|------|------|
| `ContentFab.js` | 页面内容区的悬浮按钮逻辑 |
| `Draggable.js` | 拖拽行为封装 |
| `index.js` | 导出 |

---

### 📁 `options/` —— 设置页面的 UI 组件
每个文件对应一个设置区域或交互元素：

| 文件 | 作用 |
|------|------|
| `index.js` | 设置页入口组件 |
| `About.js` | 关于页面 |
| `Apis.js` | 翻译 API 配置界面 |
| `DarkModeButton.js` | 暗色模式切换按钮 |
| `DownloadButton.js` | 导出/备份设置下载按钮 |
| `FavWords.js` | 收藏单词管理界面 |
| `Header.js` | 页头 |
| `HelpButton.js` | 帮助按钮 |
| `InputSetting.js` | 输入框相关设置 |
| `Layout.js` | 布局组件 |
| `MouseHover.js` | 悬停翻译设置 |
| `Navigator.js` | 导航栏 |
| `Playground.js` | 翻译效果预览或试验台 |
| `ReusableAutocomplete.js` | 可复用自动补全组件 |
| `Rules.js` | 规则配置 UI |
| `Settings.js` | 通用设置面板 |
| `ShortcutInput.js` | 快捷键录入组件 |
| `ShowMoreButton.js` | “展开更多”按钮 |
| `StylesSetting.js` | 自定义样式设置 |
| `Subtitle.js` | 字幕翻译相关设置 |
| `SyncSetting.js` | 同步设置 |
| `Tranbox.js` | 翻译弹窗样式/行为设置 |
| `UploadButton.js` | 设置导入上传按钮 |

---

### 📁 `popups/` —— 弹出面板 UI
| 文件 | 作用 |
|------|------|
| `Headers.js` | 弹出面板头部 |
| `index.js` | 弹出面板入口 |
| `PopupCont.js` | 弹出面板内容容器 |

---

### 📁 `selection/` —— 划词翻译交互组件
用户在页面上选中文本后出现的所有功能入口：

| 文件 | 作用 |
|------|------|
| `index.js` | 划词工具栏入口 |
| `AuditBtn.js` | 审查/修改译文按钮 |
| `CopyBtn.js` | 复制译文或原文按钮 |
| `DictCont.js` | 词典内容展示 |
| `DictHandler.js` | 词典逻辑控制 |
| `DraggableResizable.js` | 可拖拽可调整大小的容器 |
| `FavBtn.js` | 收藏按钮 |
| `SugCont.js` | 翻译建议内容 |
| `TranBox.js` | 翻译结果展示框 |
| `TranBtn.js` | 触发翻译的按钮 |
| `TranForm.js` | 翻译输入表单（可能允许改写） |
| `Zdic.js` | 查词结果（可能针对汉日词典） |

---

### 📁 浏览器扩展入口文件（后台、内容、页面）
这些文件对应 Chrome 扩展的重要脚本上下文：

| 文件 | 作用 |
|------|------|
| `background.js` | Service Worker（后台）主入口，常驻后台处理网络请求、快捷键等 |
| `common.js` | 后台/内容脚本共享的通用代码 |
| `content.js` | 内容脚本主入口，运行在网页环境，注入 UI 和监听事件 |
| `index.js` | 可能是 content 的入口模块聚合 |
| `injector-shadowroot.js` | 应用 Shadow DOM 隔离的注入逻辑入口 |
| `injector-subtitle.js` | 字幕翻译注入实现 |
| `options.js` | 设置页面（options.html）的脚本入口 |
| `popup.js` | 弹出窗口（popup.html）的脚本入口 |
| `rules.js` | 规则导出入口（可能被 bg 或 content 使用） |
| `shortcut.js` | 快捷键初始化入口 |
| `storage.js` | 存储监听或迁移入口 |
| `stylesheet.js` | 样式表生成入口 |
| `text.js` | 多语言文本条目的集中管理 |
| `theme.js` | 主题初始化入口 |
| `tooltip.js` | 提示条功能的入口 |
| `upload-button.js` | 上传按钮的单独入口（也许用于特殊页面） |

---

整个插件采用的是**模块化、组件化的扩展架构**：将翻译请求、缓存、UI 面板、设置、注入、规则引擎等完全解耦，便于维护和扩展新的翻译源或交互方式。