# mihomo-rules
## Personal collection — for private use only.

这份规则集按软路由 mihomo 配置里用到的 rule-providers 整理，发布在 [nibs-taut9m/mihomo-rules](https://github.com/nibs-taut9m/mihomo-rules)。

快照日期：2026-11-14（America/Los_Angeles）

## 目录

- `classical/`：完整经典规则（YAML）。含域名、IP，以及无法写入 mrs 的条目。`behavior: classical`，`format: yaml`。
- `domain/`：域名规则的 YAML 与官方/转换后的 MRS。`behavior: domain`。
- `ipcidr/`：IP 规则的 YAML 与官方/转换后的 MRS。`behavior: ipcidr`。
- `upstream/`：公共 geo 名单的原始备份（QuixoticHeart/rule-set、MetaCubeX/meta-rules-dat），保留官方文件名和二进制 mrs。

mihomo 的 `.mrs` 目前只支持 `domain` 和 `ipcidr`，不支持 `classical`。所以混合了域名和 IP 的组无法做成「一个 classical mrs」。这类组请二选一：

1. 推荐：直接用 `classical/组名.yaml`，一条 `RULE-SET` 即可，规则最完整。
2. 若坚持用 mrs：同时引用 `domain/组名.mrs` 和 `ipcidr/组名.mrs`（没有 IP 的组只有前者）。

下列条目无法写入 mrs，只存在于对应的 classical YAML：

- apple: DOMAIN-KEYWORD,apple.com.edgekey.net
- onedrive: DOMAIN-KEYWORD,-my.sharepoint.com

## 公共 geo 备份

下列公共 geo 名单使用与 rule-providers 相同的名称，便于直接引用：

- `fakeipfilter-domain` ← QuixoticHeart `fake-ip-filter`
- `cn-domain` ← QuixoticHeart `cn`
- `private-domain` ← MetaCubeX geosite `private`
- `private-ip` ← MetaCubeX geoip `private`
- `cn-ip` ← MetaCubeX geoip `cn`

原始文件和校验和见 `upstream/SNAPSHOT.txt`。这些名单的版权归原项目，本仓库只做备份。

## 配置要点

- `copilot` 必须写在 `github` 前面，否则 Copilot 专用主机会被 GitHub 后缀规则提前匹配。
- `-cn` 组默认走 DIRECT。
- `vpn-by-google` 必须写在 `google-cn`、`youtube`、`google` 前面。
- `stun-webrtc`、`ads-trackers` 建议保持在最前并 REJECT。
- 引用示例（把仓库名按实际情况替换；下列默认仓库名为 `mihomo-rules`，分支为 `main`）：

```yaml
rule-providers:
  github:
    type: http
    interval: 86400
    behavior: classical
    format: yaml
    url: https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/classical/github.yaml
    path: ./rule_provider/github
  cn-ip:
    type: http
    interval: 86400
    behavior: ipcidr
    format: mrs
    url: https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/ipcidr/cn-ip.mrs
    path: ./rule_provider/cn_ip
```

更完整的 `rule-providers` 和 `rules` 片段见 `config-snippet.yaml`。不要把带密钥的主配置上传到仓库。
