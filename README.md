# 华润赢 Hub

华润赢客户端的公共资源仓库，用于存放更新信息，并为后续公告、文档和图片等公共内容预留空间。

更新相关文件统一放在 `updates/`，后续公告、文档和图片可使用各自目录。

## 客户端更新

客户端读取 [updates/updates.json](updates/updates.json)，固定地址为：

```text
https://raw.githubusercontent.com/freephilx/huarunwin-hub/main/updates/updates.json
```

- `release`：正式渠道；`debug`：测试渠道。两者独立维护，不互相回退。
- 每个渠道的 `publishedVersion` 指向已发布版本；`null` 表示该渠道尚未发布版本。
- `releases` 内每条记录包含 `version`、非空的 `changes` 和独立的 HTTPS `downloadUrl`。版本不带 `v` 或构建号，可带 `-pre.1` 等语义化预发布标识。
- `downloadUrl` 可指向该版本的 GitHub Release 页面，后续可单独迁移至其他公开 HTTPS 下载地址。
- 提前准备高版本记录不会触发更新；只有该渠道的 `publishedVersion` 高于本机版本才会提醒。

当前尚未发布公开安装包，两个渠道的 `publishedVersion` 均为 `null`。`0.1.0` 是预备说明，其中的下载地址为本仓库计划使用的版本发布页，安装包上传前不可下载。

## 发布顺序

1. 在对应渠道的 `releases` 中准备版本说明和实际下载地址。
2. 上传安装包，确认用户能够匿名访问该版本下载地址。
3. 将该渠道的 `publishedVersion` 改为该版本并提交。不要在安装包可用之前推进此字段。

客户端本地 debug 运行默认使用测试渠道，正式构建默认使用正式渠道；采用 release 编译模式的测试包通过 `--dart-define=HUARUNWIN_UPDATE_CHANNEL=debug` 显式选择测试渠道。客户端仓库里的 `changelog.json` 用于随包保存日志和 tag 编译校验，本文件是在线更新判断的唯一来源。

## 许可

本仓库内容使用 [MIT License](LICENSE)。
