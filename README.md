# wuxianhuabu-lewei

乐薇无限画布生图工具的 Codex skill 与封装方案仓库。

这个仓库保存一个用于改造、封装和维护本地无限画布生图工具的 skill。目标是让用户安装后不需要理解代码、不需要手动配置复杂环境，就可以一键启动本地 Web 画布，并支持多图参考、角色化连线、产品图隔离和可调试的 generationPayload。

## 主要目标

- 一键启动无限画布：`infinite-canvas start`
- 支持 Windows 和 macOS
- 支持多图片节点连接到同一个生成节点
- 每张参考图独立保存 role、weight、instruction、enabled、order
- 产品图默认只锁定产品外观，不污染场景、姿势、光影和风格
- 支持自定义网关、OpenAI、Google，以及后续第三方适配器
- 提供 `doctor`、`config`、本地历史、导入导出和 HTTP API

## Skill 文件

- [`SKILL.md`](./SKILL.md)：Codex 使用的主 skill 指令
- [`references/implementation-plan.md`](./references/implementation-plan.md)：封装实施计划
- [`references/data-contracts.md`](./references/data-contracts.md)：节点、边和生成 payload 数据结构
- [`references/api-contract.md`](./references/api-contract.md)：本地服务 API 约定
- [`skill.json`](./skill.json)：本地 Web App skill 元数据草案
