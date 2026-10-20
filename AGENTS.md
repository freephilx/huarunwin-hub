# 华润赢 Hub：项目约定

## 仓库职责与相对位置

本仓库维护公开更新信息及后续公共资料，客户端源码、构建和安装包由客户端仓库维护。

| 仓库 | 相对于本仓库根目录的位置 | 职责 |
| --- | --- | --- |
| [huarunwin-hub](https://github.com/huarunwin/huarunwin-hub) | `.` | 维护 `api/updates.json` 等公开资源 |
| [huarunwin](https://github.com/huarunwin/huarunwin) | `../huarunwin` | Flutter 客户端源码、构建与 Release 安装包 |

两个仓库约定检出在同一父目录下，分别为 `huarunwin-hub/` 和 `huarunwin/`，各自独立管理 Git。从客户端访问本仓库使用 `../huarunwin-hub`。这是本地协作约定，客户端运行时读取公开地址，不依赖相邻目录；本地目录缺失时先确认检出位置。

## 跨仓库协作

- 涉及客户端更新数据格式、版本与渠道规则、下载地址或发布流程时，应联动检查双方的实现、配置与文档，并同步完成必要改动。客户端约定见 [../huarunwin/AGENTS.md](../huarunwin/AGENTS.md)，更新解析入口为 `../huarunwin/lib/data/app_changelog.dart`。
- 操作前读取各自的 `AGENTS.md`，分别检查 Git 状态并保留已有改动；提交、推送与发布按用户任务范围分别在对应仓库处理，完成时说明双方结果。
- 更新字段与发布顺序维护在 [README.md](README.md)，客户端更新流程见 [../huarunwin/docs/self-updating.md](../huarunwin/docs/self-updating.md)。修改协议时同步维护这些文档。
- 客户端仅依据 `api/updates.json` 中各渠道的 `publishedVersion` 判断在线更新，安装包托管于客户端仓库 Releases。先确认对应版本安装包可用，再推进发布指针；尚未发布的渠道保持 `publishedVersion: null`。`api/updates.json` 同时是客户端日志展示、tag 前置校验及 Release 说明的唯一数据源，客户端不维护独立日志文件。
- 创建或推送客户端正式 tag 前，先将对应版本的预备日志提交推送到本仓库 `main`：`release.releases` 须有去掉 tag 的 `v` 前缀后的精确版本、唯一记录及非空字符串数组 `changes`。`downloadUrl` 可省略，预备日志不推进 `publishedVersion`。再从客户端根目录运行 `python3 tool/ci/release.py check-tag vX.Y.Z`，匿名读取远端校验成功后才能创建并推送 tag；仅修改本地文件、只提供 debug 渠道日志或无法读取远端时均不能继续。

## 文档与验证

- 受版本控制的内容使用仓库相对路径、公开仓库链接、标准环境变量或明确的占位符，不写入开发者个人绝对路径、机器与设备信息、凭据或个人网络配置。
- 默认不运行测试，只有用户明确要求时才执行；验证结果仅记录实际执行的内容。客户端本机 Flutter／Dart 命令使用 `fvm flutter ...`／`fvm dart ...`，版本遵循客户端 `.fvmrc`；客户端 CI 由固定提交的 Flutter Action 读取同一版本文件安装 SDK，核对实际版本后直接执行 `flutter`／`dart`。
