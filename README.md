# 湖大微生活小程序 (Tsumiki 版)

## 1. 概览

本项目是 [weihuda_weapp](https://github.com/qnxg/weihuda_weapp) 前端的新版本，在保留原有功能体系的基础上，对工程化、状态管理、样式方案与项目结构进行了重新设计。设计文档与开发规范存放于 `docs/` 目录，是本项目规范的权威来源，开发前请先阅读。

### 技术栈

| 名称               | 文档地址                                                    | 备注                                          |
| ------------------ | ----------------------------------------------------------- | --------------------------------------------- |
| 微信小程序开发文档 | https://developers.weixin.qq.com/miniprogram/dev/framework/ | 微信小程序官方文档                            |
| Taro               | https://docs.taro.zone/docs/                                | 移动端解决方案 (4.1.11, vite 编译)            |
| React              | https://zh-hans.react.dev/reference/react                   | JS界面构建库                                  |
| TypeScript         | https://ts.nodejs.cn/                                       | 一种基于 JavaScript 构建的强类型编程语言      |
| pnpm               | https://pnpm.io/zh/                                         | 包管理器 (11.10.0)                            |
| clsx               | https://www.npmjs.com/package/clsx                          | 类名拼接工具 (经 `src/utils/cn.ts` 导出 `cn`) |
| @twisuki/ohday     | https://www.npmjs.com/package/@twisuki/ohday                | 自研日期时间库, 用于日期相关处理              |
| ESLint             | https://eslint.org/docs/latest/                             | 代码检查与格式化 (@antfu/eslint-config)       |
| Sass               | https://sass-lang.com/                                      | 样式兜底方案                                  |

> 旧版使用的 TailwindCSS 在本项目中已不再使用。本项目自维护一套原子类样式，配合内联样式与 SCSS 兜底构成分层样式方案，详见 [样式方案](./docs/style-scheme.md)。
> 本项目未引入任何全局状态管理库 (Redux / Zustand)，全局状态共享统一通过 React Context 完成，详见 [状态管理](./docs/state-manager.md)。

### 项目结构

本项目采用 `按页聚合` 与 `非结构化命名, 按路径分类` 的结构，具体设计见 [项目结构](./docs/structure.md)。

```shell
src
├─ apis                                # API 接口层
│  ├─ index.ts                         # API 统一导出
│  └─ models                           # API 数据模型
├─ components                          # 通用组件
│  ├─ card                             # 卡片组件 (Card / CardHeader / CardContent 等)
│  ├─ page                             # 页面容器组件 (Page / PageContent)
│  ├─ tabs                             # 标签页组件
│  └─ ...                              # 其他通用组件
├─ config                              # 配置层 (env / 颜色 / 存储键 / 日志标签等)
├─ contexts                            # 全局 React Context (auth / setting / semester)
├─ hooks                               # 通用 Hooks (请求 / 存储 / 课程 / 成绩等)
├─ libs                                # 基础库 (请求 / 鉴权请求 / 登录引导桥接等)
├─ pages                               # 主包页面, 按页聚合
│  ├─ index                            # 首页
│  ├─ toolkit                          # 工具箱
│  ├─ table                            # 课表
│  ├─ profile                          # 我的
│  ├─ feedback                         # 意见反馈
│  ├─ feedback-history                 # 反馈历史
│  └─ ...                              # 其余页面
├─ setting                             # 设置页分包
├─ tools                               # 工具页分包
├─ about                               # 关于页分包
├─ static                              # 静态资源, 内部结构与页面路径同构
├─ types                               # 类型定义
├─ utils                               # 通用工具函数
│  ├─ cn.ts                            # 类名拼接
│  ├─ ohday.ts                         # 日期时间处理 (基于 @twisuki/ohday)
│  ├─ logger.ts                        # 通用日志 (替代 console.log)
│  └─ ...                              # 其他工具函数
├─ app.config.ts                       # 小程序入口, 相当于小程序中的 app.json
├─ app.tsx                             # 入口文件
├─ index.html                          # index.html
└─ theme.json                          # 主题变量 (深浅色)
```

## 2. 快速开始

在开始前，请确认已经安装并配置好 Node.js (参考 `.nvmrc`, Node 22)，并使用 Corepack 或全局安装启用 [pnpm](https://pnpm.io/zh/) `11.10.0`。

本项目使用 pnpm 管理依赖。请不要混用 `npm` 或 `yarn`，否则可能会出现依赖相关的问题 (项目下出现 `package-lock.json` / `yarn.lock` 即表示混用了其他包管理器)。

1. 安装依赖

```shell
pnpm install
```

2. 启动项目

```shell
pnpm dev
```

运行以上命令后当前工作目录中会出现 `dist` 文件夹，这是项目的打包结果，同时会监听文件变化，每次文件发生变化时都会重新打包。构建完成后，你需要：

1. 打开微信开发者工具
2. 点击右上角的 `导入`
3. 选择 `dist` 目录
4. 在后端服务中选择 `不使用云开发`
5. 点击确定按钮

注意：

- 项目默认使用游客 appid (`touristappid`) 编译，无需注册小程序即可导入开发调试。若要使用完整的小程序能力 (登录等)，可将 `project.config.json` 中的 `appid` 替换为你的小程序 appid，并找管理员在微信公众平台中将你添加为开发者。
- 使用本地或 Mock 接口时，若遇到 “url not in domain list” 类的报错，需要在微信开发者工具 - 详情 (右上角) - 本地设置中勾选 `不校验合法域名、web-view(业务域名)、TLS版本以及HTTPS证书`。

### 3. 本地 Mock 接口配置

前端开发需要使用 Apifox 来提供一套临时接口供前端调用。你可以按以下步骤配置 Mock 接口。

1. 前往 [Apifox 网站](https://apifox.com) 安装 Apifox 或使用 Web 版
2. 前往 QQ 群索要 Apifox 接口文档邀请链接
3. 接受邀请后，左侧「我的团队」中会出现「易千」，点击进入后，右侧选择「微生活 API v2 (Tsumiki)」
4. 页面右上角点击三条横线图标，找到云端 Mock，复制「默认模块」的「前置 URL」的值，并将其填入 `.env` 文件的 `TARO_APP_BASE_URL` 处，例如 `TARO_APP_BASE_URL="https://example.com/xxxxxx"`

## 3. 本地环境配置

项目下的 `.env` 文件定义了一些影响编译结果的变量，如后端 URL `TARO_APP_BASE_URL`、日志级别 `TARO_APP_LOG_LEVEL`、存储过期时间 `TARO_APP_STORAGE_EXPIRED_TIME` 等。

`.env` 文件已被 `.gitignore` 排除，不会提交到远程。需要查看可用的环境变量可以参考 `.env-example`：

```env
TARO_APP_BASE_URL=http://localhost:8080
TARO_APP_LOG_LEVEL=4
TARO_APP_STORAGE_EXPIRED_TIME=604800000
```

各环境变量的含义与默认值见 `src/config/env.ts` 的 `ENV` 配置。

## 4. 测试

微信开发者工具提供了 `模拟器` 功能，可以对小程序界面进行模拟。模拟器底部有 `打开webview调试页` 的按钮，可以检查网络请求、样式表、元素、控制台输出等，使用方法和浏览器的开发者工具相同。注意：快捷键 F12 打开的是微信开发者工具本身的调试页，无法用于调试小程序。

某些情况下，模拟器和真机的行为并不相同。如果想要在真机上测试，可以点击工具栏上的 `预览` 按钮，或者使用快捷键 `Ctrl+Shift+P`，然后使用真机上的微信扫描生成的二维码即可。开始真机调试后可能会出现类似网络请求无法完成的问题，可以在右上角的更多按钮里选择 `开发调试` > `打开调试`，如果不是小程序本身的 bug 的话就能解决。

## 5. 代码检查与格式化

### 代码检查——`check` / `lint` 脚本

TypeScript 通过添加静态类型检查让开发者在编译阶段就能捕捉到很多潜在的错误，ESLint 则用于发现和修复潜在的语法错误、代码风格问题以及可能导致 bug 的不规范写法。

本项目配置了 TypeScript 与 ESLint 规则。如果你使用的是 VSCode，建议安装 ESLint 插件，减少引入 bug 的可能。在命令行中触发代码检查：

```shell
pnpm check   # tsc --noEmit, 类型检查
pnpm lint    # eslint ., 代码检查
pnpm fix     # eslint . --fix, 自动修复
```

本项目使用 `@antfu/eslint-config`，代码风格为双引号、2 空格缩进、无分号，具体见 `eslint.config.mjs`。格式化由 ESLint 完成 (配置了 markdown / css / html 等格式校验)，无需额外安装 Prettier。

代码规范提醒：

- 禁止直接使用 `console.log` 输出日志，需要日志时使用 `src/utils/logger.ts` 的 `logger`。
- 公共类型 / 函数 / Hook 应编写 JSDoc / TSDoc 注释，注释与文档使用中文，标点统一用半角。

### 提交前检查——Git Hooks

项目配置了 [Husky](https://typicode.github.io/husky/) Git Hooks：

- `pre-commit`: 提交前自动运行 `pnpm check` 与 `pnpm lint`。
- `commit-msg`: 通过 [Commitlint](https://commitlint.js.org/) 校验提交信息是否符合 Conventional Commits。

## 6. 开发规范

项目规范统一维护在仓库根目录的 [`AGENTS.md`](./AGENTS.md) 与 `docs/` 目录下，改动代码前请先阅读对应文档：

- [`docs/index.md`](./docs/index.md) — 文档总入口
- [`docs/structure.md`](./docs/structure.md) — 项目结构 (按页聚合 + 按路径分类)
- [`docs/state-manager.md`](./docs/state-manager.md) — 状态管理 (Context + Hook 状态业务分离)
- [`docs/style-scheme.md`](./docs/style-scheme.md) — 样式方案 (原子类 + 内联 + SCSS 兜底)
- [`docs/common-function.md`](./docs/common-function.md) — 通用组件 / 函数 / Hook 清单

简要约定如下：

- **提交规范**: 提交信息遵循 Conventional Commits，描述使用中文；分支命名为 `title/scope/description-author-MMDD`；不要直接提交到 `main`；与主分支同步时优先使用 rebase；合并 PR 时使用 squash merge。
- **目录约定**: 全局通用内容放 `src/` 顶层对应目录，某页专用内容放该页目录下；页面即路由文件夹，新增页面须注册到 `src/app.config.ts`。
- **命名约定**: 文件仅以 Feature 命名，不用层级 / 功能后缀，用路径区分功能。
- **状态管理**: Context 只放最基本的状态变量与 setter，复杂业务逻辑下沉到对应 Hook；`setState` 用到原值时必须函数式更新。
- **样式方案**: 优先使用原子类 (经 `cn` 拼接)，其次内联 `style`，最后 SCSS 兜底；不硬编码主题色。
- **依赖与供应链**: `pnpm-workspace.yaml` 启用了 `trustPolicy: no-downgrade`，新增依赖时优先选择带 provenance 证明的版本。

## 7. CI

CI 工作流位于 `.github/workflows`，基于 GitHub Actions：

### PR 检查——`checker.yml`

在向 `main` 分支提交 PR (以及合并到 `main` 的 push) 时自动运行，检查内容包括类型检查 (`pnpm check`)、ESLint (`pnpm lint`) 与提交信息规范 (Commitlint)。若检查不通过会进行标识。

### AI 代码评审——`reviewer.yml`

基于 Claude Code Action (指向 MiniMax M3) 的 PR 自动评审工作流，支持行内评论与总体总结。当前暂未启用，等待更稳定的方案后会有条件地触发。

## 8. 部署

上线之前你需要：

1. 确保 `.env` 文件配置正确，`TARO_APP_BASE_URL` 指向正式环境，日志级别调整到生产适合的级别。
2. 在微信开发者工具的设置中取消勾选 `不校验合法域名` 选项，然后对你所写的功能进行测试。

运行以下命令对项目打包：

```shell
pnpm build
```

点击微信开发者工具右上角的 `上传` 按钮，之后在微信公众平台中就可以看到新上传的小程序版本。小程序在发布前需要先提交到微信官方审核，提交审核时只需要提供一个小程序的首页截图即可通过审核。

通过审核之后小程序不会自动发布，需要再自己从后台手动点击发布。
