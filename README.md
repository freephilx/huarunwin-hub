# 华润赢 Hub

[华润赢组织](https://github.com/huarunwin)维护的公共资源仓库，为[客户端](https://github.com/huarunwin/huarunwin)存放更新信息，并为后续公告、文档和图片等公共内容预留空间。

供客户端读取的静态 JSON 文件统一放在 `api/`，后续公告、文档和图片可使用各自目录。

## 客户端更新

客户端读取 [api/updates.json](api/updates.json)，固定地址为：

```text
https://raw.githubusercontent.com/huarunwin/huarunwin-hub/main/api/updates.json
```

- `release`：正式渠道；`debug`：测试渠道。两者独立维护，不互相回退。
- 每个渠道的 `publishedVersion` 指向已发布版本；`null` 表示该渠道尚未发布版本。
- `releases` 内每条记录包含 `version` 和非空的 `changes`。版本不带 `v` 或构建号，可带 `-pre.1` 等语义化预发布标识。
- 版本记录的 `downloadUrl` 可省略或为 `null`，客户端默认使用 `https://github.com/huarunwin/huarunwin/releases/tag/v<version>`，例如 `0.1.0` 对应 `/releases/tag/v0.1.0`。显式 HTTPS 地址优先，可配置发布页或实际安装包直链；空字符串、类型错误或无效地址仍报错，不回退到默认地址。
- 默认规则只生成发布页地址，客户端仍解析真实附件；可选 `packages` 内各项安装包的 `downloadUrl` 必填，顶层字段可继续省略。发布页或本机安装包不存在时更新失败，不拼接附件地址。
- 正式 Git tag 使用 `vX.Y.Z`，例如 `v0.1.0`；本文件的 `version` 和 `publishedVersion` 仍写 `0.1.0`。dev 渠道保留固定 `pre` tag，应用版本可为 `0.1.0-pre.运行序号`；每条在线更新记录的下载地址须绑定该完整版本的实际安装包，不能把会被覆盖的 pre Release 页面用于固定版本更新。
- 提前准备高版本记录不会触发更新；只有该渠道的 `publishedVersion` 高于本机版本才会提醒。

默认发布页保留完整版本，例如 `0.1.0-pre.42` 对应 `/releases/tag/v0.1.0-pre.42`，不自动改为 `/releases/tag/pre`。当前 dev 预发布使用固定 `pre` tag，测试渠道应通过显式版本安装包直链或 `packages` 指向实际资源。尚不支持该默认规则的旧客户端仍要求顶层 `downloadUrl`，兼容期间继续保留该字段。

安装包由[客户端仓库的 Releases](https://github.com/huarunwin/huarunwin/releases)托管，本仓库保存更新信息。两个渠道的当前发布版本以 `api/updates.json` 中各自的 `publishedVersion` 为准。正式版本省略 `downloadUrl`，使用对应的默认发布页。后续版本继续先准备日志，待安装包发布并核验后再推进发布指针。

## 发布顺序

1. 正式 tag 创建前，在 `release.releases` 中准备去掉 `v` 前缀的精确版本及非空字符串数组 `changes`，版本记录必须唯一，并先提交推送到本仓库 `main`。使用默认发布页时可省略 `downloadUrl`，其他来源填写实际下载地址；保留原 `publishedVersion`，首次发布保持 `null`。
2. 在相邻客户端仓库根目录执行 `python3 tool/ci/release.py check-tag vX.Y.Z`，确认匿名可读的远端 Hub 记录有效后，再创建并推送正式 tag。仅修改本地 Hub 文件不算就绪；读取失败、版本缺失或日志无效时停止。正式 tag 的 Actions 会在安装工具链前重复校验，dev／PR 不要求这一步。
3. 构建并上传安装包，确认用户能够匿名访问该版本的默认发布页或显式下载地址及实际附件。
4. 将该渠道的 `publishedVersion` 改为该版本并提交推送。不要在安装包可用之前推进此字段。

客户端本地 debug 运行默认使用测试渠道，正式构建默认使用正式渠道；采用 release 编译模式的测试包通过 `--dart-define=HUARUNWIN_UPDATE_CHANNEL=debug` 显式选择测试渠道。本文件是客户端更新判断、日志展示、tag 前置校验及 Release 说明的唯一数据源，不在客户端仓库或安装包中维护日志副本。正式发布取对应 `release` 版本日志；dev 发布只取 `debug` 渠道对应完整预发布版本的日志，未准备时省略日志段。

## 许可

本仓库内容使用 [MIT License](LICENSE)。
