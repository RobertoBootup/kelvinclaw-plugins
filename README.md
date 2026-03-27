# kelvinclaw-plugins

Distribution repository for prebuilt, signed KelvinClaw plugin packages.

This repo is intentionally focused on publish/install artifacts, not plugin source code:

- `index.json` (plugin index, schema `v1`)
- `packages/<plugin_id>/<version>/<plugin_id>-<version>.tar.gz`
- `trusted_publishers.kelvin.json` (publisher trust policy)

## For Plugin Developers

Use this guide first:

- [docs/PLUGIN_AUTHOR_GUIDE.md](docs/PLUGIN_AUTHOR_GUIDE.md)
- [docs/LOCAL_SIGNING_AND_PUBLISHING.md](docs/LOCAL_SIGNING_AND_PUBLISHING.md)

All plugins are signed with Ed25519 keys. AgenticHighway first-party releases
are signed via CI. Community publishers use the PEM-based signing flow
documented in the guides above.

For local community development, use the public authoring flow in the
`kelvinclaw` repo first. Unsigned local plugins are supported there and Kelvin
warns on install instead of blocking them. This repo is only for published
package artifacts and trust/index metadata.

Repo automation:

- `.github/workflows/validate-repository.yml`
- `.github/workflows/publish-first-party-package.yml`
- `scripts/validate-repository.sh`
- `scripts/refresh-first-party-package.sh`

Templates:

- [templates/plugin.tool.wasm_tool_v1.json](templates/plugin.tool.wasm_tool_v1.json)
- [templates/plugin.model.wasm_model_v1.json](templates/plugin.model.wasm_model_v1.json)
- [templates/index.entry.v1.json](templates/index.entry.v1.json)

## KelvinClaw References

This repo does not duplicate SDK/runtime specification docs. Canonical references:

- [KelvinClaw: Plugin Install Flow](https://github.com/agentichighway/kelvinclaw/blob/main/docs/plugins/plugin-install-flow.md)
- [KelvinClaw: Model Plugin ABI](https://github.com/agentichighway/kelvinclaw/blob/main/docs/plugins/model-plugin-abi.md)
- [KelvinClaw: Plugin Index Schema](https://github.com/agentichighway/kelvinclaw/blob/main/docs/plugins/plugin-index-schema.md)
- [KelvinClaw: Trusted Executive + WASM](https://github.com/agentichighway/kelvinclaw/blob/main/docs/architecture/trusted-executive-wasm.md)

## Installing Plugins From This Repo

Set `KELVIN_PLUGIN_INDEX_URL` to this repo's raw index URL, then use `kpm` from a KelvinClaw release bundle:

```bash
export KELVIN_PLUGIN_INDEX_URL=https://raw.githubusercontent.com/agentichighway/kelvinclaw-plugins/main/index.json

./kpm search                       # list available plugins
./kpm install kelvin.anthropic     # install a plugin
./kpm install kelvin.cli           # install a specific plugin
./kpm list                         # list installed plugins
./kpm update                       # update all installed plugins
```

The underlying install script (`share/scripts/plugin-index-install.sh`) is also available directly in KelvinClaw release bundles for scripted or CI use.
