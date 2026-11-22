# mihomo-rules
## Personal collection — for private use only.

这份规则集按软路由 mihomo 配置里用到的 rule-providers 整理，发布在 [nibs-taut9m/mihomo-rules](https://github.com/nibs-taut9m/mihomo-rules)。

快照日期：2026-11-14（America/Los_Angeles）

## 目录

- `classical/`：完整经典规则（YAML）。含域名、IP，以及无法写入 mrs 的条目。`behavior: classical`，`format: yaml`。
- `domain/`：域名规则的 YAML 与官方/转换后的 MRS。`behavior: domain`。
- `ipcidr/`：IP 规则的 YAML 与官方/转换后的 MRS。`behavior: ipcidr`。
- `upstream/`：公共 geo 名单的原始备份（QuixoticHeart/rule-set、MetaCubeX/meta-rules-dat），保留官方文件名和二进制 mrs。
- `shadowrocket/`：与上述规则组对应的 Shadowrocket `.list`（不含策略）。在配置里用 `RULE-SET` 引用，并自行指定 DIRECT / PROXY / REJECT。

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

## Shadowrocket

`shadowrocket/` 提供与 mihomo 规则组同名的 `.list` 文件，由本仓库 2026-11-14（America/Los_Angeles）快照转换而来，不是新的来源。列表只含匹配条件，不含 `PROXY` / `DIRECT` / `REJECT`；策略请在自己的 Shadowrocket 配置里指定。

引用地址（把组名换成实际文件名；分支为 `main`）：

    https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/组名.list

例如：

    https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/stun-webrtc.list

在 `[Rule]` 段用 `RULE-SET` 引入。把下面的 `PROXY` 换成你自己的策略组即可：

```
[Rule]
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/stun-webrtc.list,REJECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/ads-trackers.list,REJECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/private-domain.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/private-ip.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/cn-domain.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/cn-ip.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/valve-cn.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/valve.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/apple-private-relay.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/apple-intelligence.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/apns.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/apple-cn.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/apple.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/x.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/telegram.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/meta.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/bytedance.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/crypto.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/copilot.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/github.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/anthropic.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/openai.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/onedrive.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/microsoft-cn.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/microsoft.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/vpn-by-google.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/google-cn.list,DIRECT
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/youtube.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/google.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/talkatone.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/discord.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/twitch.list,PROXY
RULE-SET,https://raw.githubusercontent.com/nibs-taut9m/mihomo-rules/main/shadowrocket/gfw-common.list,PROXY
FINAL,PROXY
```

顺序要点与 mihomo 配置相同：

- `stun-webrtc`、`ads-trackers` 建议保持在最前并 REJECT。
- `copilot` 必须写在 `github` 前面，否则 Copilot 专用主机会被 GitHub 后缀规则提前匹配。
- `-cn` 组默认走 DIRECT。
- `vpn-by-google` 必须写在 `google-cn`、`youtube`、`google` 前面。

`fakeipfilter-domain.list` 是公共 geo 备份的转换结果，对应 mihomo 里的 Fake-IP 过滤名单，不是默认分流规则；源名单中 29 条带中间通配符的条目无法用 Shadowrocket 的 DOMAIN / DOMAIN-SUFFIX / DOMAIN-KEYWORD 忠实表达，已省略。
