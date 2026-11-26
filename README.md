# muse-connectors-weekly

Weekly export of Muse's built-in connectors, programmatic only (no LLM).

- Source: `/opt/hatch/connectors/` on the Muse VM (208 built-in connectors, 2026-11-26 baseline)
- Custom connectors installed by the user (e.g. Parallel) are NOT included in the 208 count.
- Schedule: every Monday ~09:00 Asia/Shanghai
- Layout: `<YYYY-MM-DD>/connectors.json` + `<YYYY-MM-DD>/connectors.md`
- Schema and dimension definitions: [SCHEMA.md](SCHEMA.md)

## What each snapshot contains

Per connector: id, title, description (verbatim), aliases, category, provenance,
require_auth, auth_method, auth_provider, cli_binary, mcp_resource_url/host,
visible_in_settings, confidential_vm, include_in_prompt, has_onboarding/sync/features/multi_account/domain,
has_manifest, has_references, doc_bytes, doc_sha256.

Provenance is a single-label, rule-based inference (priority: test > meta > vendor_official_mcp > vendor_mcp > other_api_local).
There is no unified "is official" field in the source files; see SCHEMA.md for the exact rules and the 2026-11-26 correction
(vendor_mcp 104 -> 111, other 61, after fixing a regex that missed quoted `resource: "https://..."` values).

No secrets are exported: no client_id, client_secret, tokens, or API keys.

## Latest snapshot

- 2026-11-26: 208 connectors (baseline)
  - category: productivity 41, business 26, social 10, health 8, wallet 3, unclassified 120
  - provenance: vendor_mcp 111, other_api_local 61, vendor_official_mcp 19, meta 16, test 1
