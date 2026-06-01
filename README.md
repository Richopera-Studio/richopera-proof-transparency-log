# Richopera's Proof Service 公开透明日志镜像

本仓库是 [proof.richopera.cn](https://proof.richopera.cn) 的公开透明日志 GitHub 镜像仓库。

在Gitee，也有同样的日志镜像，可以视为本仓库的国内镜像，用户或审计人员可以通过两个仓库进行交叉对比。

[点击访问国内Gitee镜像](https://gitee.com/richopera/richopera-proof-transparency-log)

Richopera Proof Service 会按 UTC 小时生成公开透明日志，记录该小时内签发过的存证证书编号、证书 JSON 哈希、签发时间、Merkle Root，以及可用时的 OpenTimestamps receipt。

这个仓库的作用，是为网站公开日志提供一个第三方公开备份位置。

## 为什么需要这个仓库

Richopera's Proof Service 网站本身已经提供公开验证页面和可下载文件，例如：

- `certificate.json`
- `transparency-proof.json`
- `log.json`
- `log.ots`

但是，如果这些文件只从 `proof.richopera.cn` 下载，它们仍然依赖网站服务方持续提供同一份数据。

把每小时透明日志同步到这个公开 GitHub 仓库后，用户和审计者就可以把网站上的数据与 GitHub 上的公开历史记录进行对照。

如果将来网站数据库、服务器文件或公开日志被修改、删除，GitHub 仓库里的历史提交仍可以作为外部公开参考。

## 目录结构

日志按 UTC 日期和小时组织：

```text
logs/
  YYYY-MM-DD/
    HH/
      log.json
      log.ots
      index.json
```

示例：

```text
logs/
  2026-06-01/
    10/
      log.json
      log.ots
      index.json
```

## 文件说明

### `log.json`

每小时的公开透明日志。

它包含：

- 日志版本
- 服务名称
- UTC 时间范围
- 证书数量
- 哈希算法
- Merkle Root
- 证书条目列表

每个证书条目包含：

- 证书编号
- 证书签发时间
- 证书 JSON 哈希
- Merkle leaf index

`log.json` 不包含作品原文、剧本文本、图片源文件、音频源文件、工程文件或其他用户私有内容。

### `log.ots`

该小时 `log.json` 的 OpenTimestamps receipt。

它可以用于验证这份日志的哈希已经提交到 OpenTimestamps，并在确认后锚定到比特币区块链。

`log.ots` 可能先以“等待确认”的状态出现，之后在 OpenTimestamps 升级后包含比特币区块信息。

### `index.json`

该小时镜像批次的元数据文件。

它记录：

- UTC 时间范围
- 证书数量
- Merkle Root
- `log.json` 哈希
- 可选的 `log.ots` 哈希
- OpenTimestamps 状态
- `proof.richopera.cn` 上的来源链接
- 本批次包含的镜像文件名

## 隐私边界

这个仓库是公开仓库，公开是它的设计目标。

它可能公开：

- 证书编号
- 证书 JSON 哈希
- 证书签发时间
- 每小时证书数量
- Merkle Root
- OpenTimestamps receipt
- 服务的公开活跃时间线

它不会公开：

- 原创作品内容
- 剧本文本
- 图片、音频或模型源文件
- 私有项目文件
- 用户密码或 API Key
- 服务端签名私钥

Richopera's Proof Service 是基于哈希的存证服务。可信数据层是签名后的证书 JSON 和公开透明日志，而不是上传作品原文。

## 如何验证

用户可以使用三份文件验证某张证书是否被包含在公开透明日志中：

1. `certificate.json`
2. `transparency-proof.json`
3. 对应小时的 `log.json`

验证过程会检查：

1. `certificate.json` 的哈希是否等于 `transparency-proof.json` 中记录的证书哈希；
2. `transparency-proof.json` 是否能重新计算出 Merkle Root；
3. 重新计算出的 Merkle Root 是否等于 `log.json` 中的 Merkle Root；
4. `log.json` 的哈希是否等于 `index.json` 中记录的哈希；
5. 如果存在 `log.ots`，则可以继续验证 OpenTimestamps 第三方时间戳。

公开验证页面：

```text
https://proof.richopera.cn/verify
```

每小时公开日志页面：

```text
https://proof.richopera.cn/logs/YYYY-MM-DD/HH
```

## 信任模型

这个仓库通过把公开日志历史从网站本身分离出来，提高存证记录的可核验性。

整体信任链路可以理解为：

```text
证书 JSON
  ↓
服务端 Ed25519 签名
  ↓
透明度证明
  ↓
每小时 Merkle Root
  ↓
OpenTimestamps receipt
  ↓
GitHub 公开镜像
```

## 许可证

本仓库中的公开透明日志数据采用 CC0 1.0 Universal 发布。

在法律允许的范围内，Richopera 放弃本仓库公开透明日志数据的版权及相关权利限制，允许任何人复制、保存、再发布、审计和长期归档。

该许可适用于本仓库中的镜像日志文件、索引文件和时间戳 receipt 文件。

该许可不涉及任何原创作品内容，因为本仓库不保存原创作品原文、图片、音频、模型或其他源文件。

这个机制并不表示 Richopera's Proof Service 是法律公证平台、版权登记机构或最终法律证明提供方。

它提供的是一条技术证据链：证明某个证书哈希曾由该服务签发，并被纳入公开、可验证、带第三方时间锚点的透明日志中。

## 重要说明

本仓库用于保存面向公众的、追加式的证据记录。

GitHub 的提交历史会被保留。即使某个文件从最新分支中删除，也不代表它一定会从 Git 历史中消失。

请不要向本仓库手动提交任何私密材料。自动镜像程序只会发布由 Richopera Proof Service 生成的透明日志数据。
