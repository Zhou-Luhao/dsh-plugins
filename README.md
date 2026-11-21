# dsh-plugins

> DeepSeek Harness（DSH）插件合集。

每个插件都是一个自包含的 bundle 目录：`package.json` 声明 `dsh.bundle.patch`，`cordis.patch.yml` 把插件行插入当前 Profile，`index.js` 实现能力，`locale/` 提供插件列表里的标题与描述，`icon.svg` 是插件图标。

**仓库目前还是空壳，尚未收录插件。** 第一个插件落地后，下表与目录会同步更新。

## 插件目录

| 插件 | 说明 | 类型 | 依赖 |
|---|---|---|---|
| — | 暂无，等待第一个插件 | — | — |

## 仓库结构

```
dsh-plugins/
├── README.md                 # 本文件：插件索引与约定
└── <plugin-name>/
    ├── package.json          # bundle 清单：name / exports / files / dsh.bundle.patch
    ├── cordis.patch.yml      # 插入插件行（可带默认 config）
    ├── index.js              # Host 插件入口：apply(ctx, config)
    ├── client.js             # 可选：Web Client 入口（需要 UI 时）
    ├── locale/
    │   ├── en.json           # meta.title / meta.description / meta.detail
    │   └── zh.json
    ├── docs/                 # 可选：DETAIL.md + 详情图
    ├── icon.svg              # 插件图标：36×36，≤ 256 KiB
    ├── test/                 # node:test 单元测试
    └── README.md             # 面向人的说明：能力、安装、配置、限制
```

## 安装插件

插件是一个目录包，装进当前 Profile 后对所有会话生效：

1. **图形界面**：DSH → 插件（Plugins）→ 添加插件（Add plugin）→ 选择插件目录。
2. **让代理安装**：让代理调用 `plugin_manager` 的 `install_bundle`，`target` 传插件目录的绝对路径。
3. 安装后在新会话里验证：Host 插件看工具是否出现，Client 插件看对应界面位置。

升级已安装插件的代码后通常需要重启 DSH 才会加载新的 JavaScript 模块。

## 新增插件

1. 在仓库根目录建 `<plugin-name>/`，按上面的结构补齐文件。
2. `package.json` 使用 `@dsh-plugins/<plugin-name>` 作为包名，声明 `private: true`、`type: module`，并在 `exports` 中导出 `.`、`./locale/*.json`、`./icon`；`files` 要包含运行时文件、patch、locale、icon 与 `docs`。
3. `cordis.patch.yml` 里的 `id` 与 `name` 和清单保持一致，默认 `config` 写在 patch 里。
4. `locale/en.json` 与 `locale/zh.json` 必须提供 `meta.title`、`meta.description`；description 说明能力与入口，不要留下模板文案。
5. 图标用原始或可再分发的素材，36×36，≤ 256 KiB；需要详情页时再加 `docs/DETAIL.md`（只放一行详情图）与图片。
6. 为纯逻辑写 `test/*.test.js`（`node:test`），运行 `node --test <plugin-name>/test`。
7. 在本文件「插件目录」表格里加一行，并把插件写进根 README 的索引。

## 开发约定

- **零运行时依赖**：插件不 import `@deepseek-ai/*` 内部包，只用公开接口（如 `ctx.tools.register`、`ctx.on`、`ctx.effect`、`ctx.inject`），这样不必把插件和某个 Harness 版本绑死。
- **能力边界写在明处**：会执行外部命令、读取文件、联网、写 Profile 的插件，必须在 `meta.description` 与插件 README 里说明。
- **配置走 `config`**：可调参数放进插件行的 `config`，用户的 Profile 补丁层在升级后仍然保留。
- **失败要可见**：外部命令缺失、平台不支持时返回可执行的错误信息，不要静默成功。
- **中英文都要**：`locale/en.json` 与 `locale/zh.json` 同时提供。

## 兼容性

插件针对 **DSH 0.2.2-rc.5** 验证。DSH 升级后需要重新验证已收录插件，并把验证过的版本写进插件 README。

## 许可

[MIT](LICENSE) © 2026 ZhouLuhao