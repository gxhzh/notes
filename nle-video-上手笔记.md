# nle-video 上手笔记

## 1. 先建立整体认知

这个项目是一个 React + Vite 的前端项目，但核心不是普通页面，而是一个视频编辑器运行时。

推荐先按这条链路理解：

```text
src/main.tsx
  -> src/app/router/index.tsx
  -> ControllerOwnerRoot
  -> ControllerDomainHost
  -> NLEController
  -> app runtime / project runtime
  -> UI bindings / stores / pages
```

关键入口：

- `nle-video/package.json`
- `nle-video/docs/architecture/system-overview.md`
- `nle-video/src/main.tsx`
- `nle-video/src/app/router/index.tsx`
- `nle-video/src/app/router/studio-routes/index.tsx`
- `nle-video/src/app/composition/app-shell/controller-owner-root/index.tsx`
- `nle-video/src/app/composition/domain/controller-domain/ControllerDomainHost.tsx`
- `nle-video/src/controller/NLEController.ts`

## 2. 目录分层

`src` 下面主要分层：

- `app`: 应用级组合层，包括路由、shell、全局 store、i18n、配置。
- `controller`: UI 和底层业务运行时之间的控制器边界。
- `core`: 视频编辑器核心，包括时间轴、播放、资源、字幕、导出等运行时逻辑。
- `infra`: 基础设施和外部服务，包括存储、接口、媒体资源、服务客户端等。
- `shared`: 跨层共享的类型、常量、工具、样式。
- `ui`: 页面和组件。新版编辑器主要在 `ui/next-editor`。

## 3. 最重要的架构认知

`NLEController` 是 UI 访问业务能力的统一入口。

通常不要从 UI 组件直接跳到 `core` 改底层逻辑。更常见的链路是：

```text
UI 组件
  -> useXxxActions / useXxxBinding
  -> controller contract
  -> NLEController
  -> project runtime / app runtime
  -> core / infra
```

`ControllerDomainHost` 负责创建唯一的 `NLEController` 实例，并通过 context 提供给路由树下的页面。

## 4. 页面入口

路由分两大类：

- 站点页面：`src/ui/site`
- 工作台/编辑器页面：`src/ui/next-editor`

重点看：

- `src/app/router/index.tsx`
- `src/app/router/studio-routes/index.tsx`
- `src/app/composition/app-shell/workspace-shell/WorkspaceAppShell.tsx`

编辑器相关页面入口大多从 `src/ui/next-editor/pages` 开始。

## 5. 后续排查问题的定位方式

登录、注册、会话问题：

- `src/app/composition/app-shell/*/auth-session`
- `src/app/stores/runtime/useSessionStore`
- `src/infra/services`

路由、页面跳转问题：

- `src/app/router`
- `src/shared/constants/routes`

项目打开、恢复、保存问题：

- `src/app/composition/app-shell/workspace-shell/WorkspaceAppShell.tsx`
- `src/app/composition/app-shell/workspace-shell/bindings/project.binding.ts`
- `src/controller/project-runtime`

时间轴、剪辑、播放问题：

- `src/controller/project-runtime`
- `src/core/engine`

素材导入、上传、处理状态问题：

- `src/infra/media`
- `src/core/runtime`
- `ResourceCatalog`
- `UploadOrchestrator`

UI 显示和交互问题：

- `src/ui/next-editor`
- 相关 hooks / actions / bindings

## 6. 建议学习顺序

1. 读 `docs/architecture/system-overview.md`，先理解总图。
2. 读 `main.tsx` 和 `router`，知道应用怎么启动、页面怎么挂载。
3. 读 `ControllerOwnerRoot`、`ControllerDomainHost`、`NLEController`，理解 controller 边界。
4. 读 `WorkspaceAppShell`，理解工作台如何恢复项目、挂载编辑器能力。
5. 从 `ui/next-editor/pages` 选择一个页面，顺着组件、actions、bindings 往下追。
6. 遇到具体 bug 时，再进入 `controller/project-runtime` 和 `core`。

## 7. 常用命令

```bash
npm run dev
npm run typecheck
npm run lint
npm run test:run
npm run build
```

项目还有架构边界检查：

```bash
npm run check:controller-semantic-boundary
npm run check:controller-project-runtime-boundary
npm run check-all
```

## 8. 大文件夹架构说明

### 8.1 顶层目录

`src`

项目业务源码主目录。绝大多数 bug 修复和功能开发都会在这里完成。

`public`

静态资源目录。Vite 会把这里的文件按原样暴露给浏览器访问。适合放不需要经过打包处理的资源，例如字体、vendor 脚本、favicon、manifest、worker 脚本、固定示例资源。

`docs`

项目文档目录。当前最重要的是 `docs/architecture/system-overview.md`，用于理解系统架构、模块边界和数据流。

`tools`

工程脚本目录。当前包含 controller 边界检查脚本，用来防止架构层级被破坏。

`test`

独立测试/验证 demo 目录。当前主要和 mediabunny、音频提取、波形内存等实验验证有关。

`docker`

容器化部署相关配置。

`patches`

`patch-package` 使用的依赖补丁目录。安装依赖后会通过 `postinstall` 应用补丁。

`openspec`

项目规格/规范相关目录，通常用于记录需求、设计约束或协作规范。

`.agents`、`.claude`

AI/协作工具相关配置和技能文件，不属于运行时代码。

### 8.2 src 目录

`src/app`

应用装配层。负责“把项目跑起来”，包括路由、全局 shell、应用级配置、i18n、全局 store、controller 注入等。

它不应该承载大量底层视频编辑算法，而是负责把 UI、controller、runtime、store 按正确方式连接起来。

`src/controller`

控制器边界层。它是 UI 和底层 core/infra 之间的门面。

UI 通常不直接访问 `core`，而是通过 `NLEController` 暴露的能力调用业务逻辑。这样可以限制 UI 能访问的能力面，降低耦合。

`src/core`

编辑器核心层。这里放时间轴、播放、资源运行时、字幕、导出、DOM 捕获等核心逻辑。

它是项目“业务真相”所在的位置，不应该依赖 UI 组件。这样核心逻辑可以独立演进、测试和复用。

`src/infra`

基础设施层。负责和浏览器能力、存储、网络服务、媒体处理、资源管理等外部依赖打交道。

它的目的通常是把“不稳定的外部世界”封装起来，避免业务核心到处散落 axios、IndexedDB、文件访问、上传处理等细节。

`src/shared`

跨层共享代码。包括常量、类型、工具函数、样式、错误定义、媒体/项目相关共享模型等。

放在这里的东西应该足够通用，不能偷偷放具体页面业务逻辑。

`src/ui`

页面和组件层。负责显示、交互、用户意图采集。

新版工作台主要在 `src/ui/next-editor`，站点页面在 `src/ui/site`，通用 UI 组件在 `src/ui/shared`。

`src/assets`

会经过构建工具处理的资源。和 `public` 的区别是：`assets` 通常被源码 import，参与打包、hash、优化。

`src/types`

全局类型声明。当前有类似第三方库声明补充文件。

`src/utils`

少量工具脚本或性能基准相关代码。和 `shared/utils` 的边界需要结合具体文件判断。

## 9. app 内部目录说明

`src/app/router`

路由定义。决定 URL 对应哪个页面、哪些页面挂在 Site Shell 下、哪些页面挂在 Workspace Shell 下。

`src/app/composition`

应用组合层。负责把 controller、shell、domain service、bindings、页面 outlet 组合起来。

重点子目录：

- `app-shell`: 应用外壳，包括站点 shell、工作台 shell、controller owner。
- `domain`: 应用级领域服务和 controller domain，包括 controller context、contracts、project open/catalog 等服务。

`src/app/stores`

应用级 Zustand store。通常保存 session、运行时投影状态、workspace 状态等 UI 可消费状态。

`src/app/config`

应用配置，例如第三方登录、环境开关等。

`src/app/i18n`

国际化初始化与语言资源装配。

## 10. controller 内部目录说明

`src/controller/NLEController.ts`

最重要的控制器入口。它把 app runtime 和 project runtime 的能力整合成 UI 可调用的 API。

`src/controller/app-runtime`

应用级运行时。偏全局能力，例如登录会话、环境配置、应用服务等。

`src/controller/project-runtime`

项目级运行时。偏具体项目能力，例如项目生命周期、时间轴、素材、播放、保存、导出等。

`src/controller/bootstrap`

默认 runtime 创建和依赖装配。

`src/controller/types`

controller 层类型定义。

`src/controller/application-semantic-boundary`

语义边界相关代码，用于维持 controller 分层约束。

## 11. core 内部目录说明

`src/core/engine`

编辑器引擎主体。通常包含时间轴、播放、字幕、渲染、事件等核心运行逻辑。

`src/core/runtime`

运行时资源和生命周期管理。通常和素材、decode、播放/预览资源租约等有关。

`src/core/domain`

核心领域模型。放和业务概念强相关、但不属于 UI 的模型或规则。

`src/core/ports`

端口/接口层。用于定义 core 需要的外部能力接口，让 core 不直接依赖具体 infra 实现。

`src/core/dom-capture`

DOM 捕获相关能力，可能用于把页面/图层捕获成可渲染资源。

## 12. infra 内部目录说明

`src/infra/services`

外部服务客户端或服务封装，例如认证、项目、计费、AI、字幕等接口调用。

`src/infra/storage`

本地存储初始化与封装，例如 IndexedDB/local persistence 相关能力。

`src/infra/media`

媒体相关基础设施，可能涉及文件探测、音视频处理、上传、转码/提取等。

`src/infra/resources`

资源访问、资源库或资源生命周期相关基础设施。

## 13. ui 内部目录说明

`src/ui/site`

官网/站点类页面，例如 landing、pricing、blog、auth、legal。

`src/ui/next-editor`

新版编辑器/工作台 UI，是后续做编辑器功能最常接触的地方。

常见阅读路径：

```text
pages
  -> page-group-shared / workspace / modules
  -> hooks/actions/bindings
  -> controller contract
  -> NLEController
```

`src/ui/shared`

通用 UI 组件、基础组件封装、跨页面复用的视觉/交互组件。

## 14. public 内部目录说明

`public/fonts`

字体资源和字体 registry。因为字体可能需要直接被浏览器加载，所以放在 public。

`public/vendor`

不通过 npm import 或需要原样提供给浏览器的第三方运行时代码，例如 ffmpeg、worker、gsap、hyperframe runtime。

`public/platform-guides`

平台说明或引导图资源。

`public/__fixtures__`

示例/测试固定资源。

`public/audio-capture-processor.js`

音频捕获相关 processor 脚本。此类脚本常需要以独立文件路径被浏览器 API 加载。

`favicon.svg`、`logo.svg`、`robots.txt`、`site.webmanifest`

站点基础静态文件。

## 15. 为什么这样设计

这个项目的复杂度主要来自“视频编辑器不是普通表单页面”。

如果 UI、播放、时间轴、资源上传、持久化、接口请求全混在组件里，会导致：

- 页面组件很难维护。
- 修一个播放 bug 容易影响保存或素材。
- 新功能不知道应该挂在哪一层。
- 测试困难。
- 多个页面复用同一项目运行时会变得混乱。

当前分层的目的：

- `ui` 只负责显示和用户交互。
- `app` 负责应用装配和路由生命周期。
- `controller` 作为唯一业务能力入口，控制 UI 能碰到什么。
- `core` 保存真正的编辑器业务状态和算法。
- `infra` 封装外部服务和浏览器/媒体能力。
- `shared` 放稳定、通用、跨层复用的东西。

这样做的好处：

- 定位 bug 更容易，可以按链路追踪。
- 新功能更容易找到归属层。
- UI 改版时，不必重写核心编辑器逻辑。
- 核心逻辑可以独立测试。
- controller contract 可以限制错误依赖，避免 UI 越层访问底层。
