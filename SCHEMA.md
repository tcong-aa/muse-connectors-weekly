# Muse 自带 connectors 每周导出：维度定稿（2026-11-26 基线，208 个）

来源：/opt/hatch/connectors/（系统自带，不含自定义 Parallel）
导出：每周一 09:00（北京时间），公开库 muse-connectors-weekly，路径 YYYY-MM-DD/connectors.json + connectors.md
文件：connectors.json（全字段）+ connectors.md（汇总表 + 本周 diff）

## 1. 身份维度

| 维度 | 来源 | 类型 / 典型值（2026-11-26 实测） |
|---|---|---|
| id | connector.yaml `id` | 字符串，唯一，如 github、gmail、spotify；共 208 个 |
| title | connector.yaml `title` | 显示名，如 GitHub、Gmail |
| description | connector.yaml `description` | 1-2 句英文说明 |
| aliases | connector.yaml `aliases` | 列表，0-4 个；典型 1 个（171 个）、2 个（27 个）、0 个（5 个多行/缺失）、3-4 个（5 个） |

## 2. 分类维度

| 维度 | 来源 | 类型 / 典型值 |
|---|---|---|
| category | manifest.yaml `category` | productivity 41、business 26、social 10、health 8、wallet 3；未标注 120 个，导出记 `unclassified`，不瞎猜 |

## 3. 来源层级（推断维度，带置信度）

文件里没有统一的“是否官方”字段，按优先级单标签归类，规则写进导出脚本，变了就改规则版本号：

优先级：test > meta > vendor_official_mcp > vendor_mcp > other

| 值 | 判定规则 | 数量（2026-11-26） |
|---|---|---|
| meta | 文本含 `is from Meta` 或 `connecting through Meta` | 16 |
| vendor_official_mcp | CONNECTOR.md / yaml 明确写 `official MCP` / `official servers` | 19 |
| vendor_mcp | manifest 有 `resource: https://mcp.<厂商域名>...`（允许引号） | 111 |
| other_api_local | 以上皆无：Google/Muse API 直连、本地 CLI、本地读取等 | 61 |
| test | security_ctf | 1 |

注：带 MCP 地址的原始共 113 个，其中 2 个已被上一级（meta / official）优先归类，所以单标签后 vendor_mcp 是 111。

**纠错（2026-11-26 脚本实跑后）**：定稿时 vendor_mcp 写成 104、other 写成 68，是旧正则漏了 `resource: "https://..."` 带引号的 7 个（beehiiv、deepl、gumroad_mcp、krea、mercury、moneyforward_me、otter）。脚本改用允许引号的正则后，正确值是 vendor_mcp 111、other 61。auth_method 也以脚本输出为准：oauth_cli 148、bin_authorize_url 24（含 Google hatch_gws_auth 的 9 个，因其 authorize 值里也含 authorize-url）、api_key 2、none 33、other 1。

## 4. 登录 / 授权维度

| 维度 | 来源 | 类型 / 典型值 |
|---|---|---|
| require_auth | connector.yaml `require_auth` | true 119、false 17、未标注 72 |
| auth_method | manifest/connector `auth.authorize`（脚本归一化） | oauth_cli 148、bin_authorize_url 24（含 hatch_gws_auth 9 个）、api_key 2、none 33、other 1 |
| auth_provider | manifest/connector `auth.provider` | 基本等于 id，如 github、gmail、spotify；每个唯一 |
| cli_binary | manifest `cli.binary` | 有 201 个，192 个不同二进制名；典型如 `spotify-api`、`hatch_gws_cli`（Google 系 9 个共用） |
| mcp_resource_url | manifest `resource:` | 有 113 个，如 https://mcp.airtable.com；无 95 个 |
| mcp_resource_host | 由上提取域名 | 113 个不同 host（含同一 connector 多个 resource 的情况） |

## 5. 可见性 / 可用性维度

| 维度 | 来源 | 类型 / 典型值 |
|---|---|---|
| visible_in_settings | manifest/connector `visible_in_settings` | true 194、false 11、缺失 3 |
| confidential_vm | `availability.confidential_vm` | true 175、false 20、其余 13 未明确/为注释文本 |
| include_in_prompt | connector.yaml `include_in_prompt` | true 28、false 180 |
| onboarding | 有无 `onboarding:` 字段 | 有 10 个 |
| sync | 有无 `sync:` 字段 | 有 11 个 |
| features | 有无 `features:` 字段 | 有 15 个 |
| multi_account | 有无 `multi_account:` 字段 | 有 9 个 |
| domain | 有无 `domain:` 字段 | 有 12 个 |

## 6. 文档 / 文件维度

| 维度 | 来源 | 类型 / 典型值 |
|---|---|---|
| has_manifest | 有无 manifest.yaml | 有 205，缺失 3：apple_music、places、tailscale |
| has_references | 有无 references/ 目录 | 有 16 个 |
| doc_bytes | CONNECTOR.md 字节数 | 最小 530、中位 1980、最大 33562，总计 973,101 |
| doc_sha256 | CONNECTOR.md 的 sha256 | 用于周间 diff：hash 变了 = 文档改了 |
| doc_path | 固定 | /opt/hatch/connectors/<id>/CONNECTOR.md |

## 7. 快照 / Diff 维度（每周文件头 + diff 段）

| 维度 | 类型 / 典型值 |
|---|---|
| snapshot_date | YYYY-MM-DD，如 2026-11-30 |
| total_count | 整数，本周 208 |
| added | 上周没有的 id 列表 |
| removed | 上周有、本周没有的 id 列表 |
| changed | id 不变但以下任一变了：title、description、category、provenance、require_auth、auth_method、visible_in_settings、doc_sha256 |
| unchanged_count | 208 - added - changed（removed 不计入本周） |

## 不导出的

- OAuth client_secret、API key、token 等密钥类字段：一律不导，只导“有没有这个字段/用哪种登录方式”。
- client_id：属于公开标识，但为稳妥也不导，只导 auth_provider。
- 你自定义的 Parallel：不含在这 208 个里，单独说明，不混进本周总数。

## 待你确认

1. 上面 7 组维度够不够？要不要加 / 删？
2. provenance 的 5 档划分和优先级认不认？
3. 确认后我就按这个建公开库 muse-connectors-weekly，先导 2026-11-30 这一周（本周一），再设每周一 09:00 自动跑。
