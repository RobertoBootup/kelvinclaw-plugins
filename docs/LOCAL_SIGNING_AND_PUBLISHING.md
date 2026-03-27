# Signing And Publishing

All plugins published to this repository must be signed with an Ed25519 key.
The matching public key is committed to `trusted_publishers.kelvin.json` so that
consumers can verify package integrity offline.

AgenticHighway first-party releases are signed via a CI workflow in this repo.
Internal signing infrastructure details are documented separately.

## Rules

1. Keep private keys off-repo.
2. Commit only public keys in `trusted_publishers.kelvin.json`.
3. Never replace an old publisher id when the old signed artifacts are still
   published. Add a new publisher id instead.

## Prerequisites

1. A clone of `agentichighway/kelvinclaw` (for signing and install scripts).
2. An Ed25519 keypair for plugin signing.
3. `openssl`, `jq`, and `tar`.

## Sign A Plugin Manifest

Use the KelvinClaw signing script with your Ed25519 private key:

```bash
/path/to/kelvinclaw/scripts/plugin-sign.sh \
  --manifest /tmp/plugin-staging/plugin.json \
  --private-key /path/to/your-ed25519-private.pem \
  --publisher-id your_publisher_id \
  --trust-policy-out /tmp/your-publisher.trust.json
```

This writes:

- `plugin.sig` — the detached Ed25519 signature
- a trust-policy snippet containing your public key

## Build A Release Tarball

Required package contents:

```text
plugin.json
plugin.sig
payload/
  <entrypoint>.wasm
```

Create the tarball:

```bash
COPYFILE_DISABLE=1 COPY_EXTENDED_ATTRIBUTES_DISABLE=1 \
  tar -czf your.plugin.id-1.0.0.tar.gz -C /tmp/plugin-staging plugin.json payload plugin.sig
shasum -a 256 your.plugin.id-1.0.0.tar.gz
```

## Publish To This Repo

1. Add the tarball under `packages/<plugin_id>/<version>/`.
2. Append your publisher public key to `trusted_publishers.kelvin.json` if
   this is a new publisher id.
3. Add or update the `index.json` entry with:
    - `id`
    - `version`
    - `package_url`
    - `sha256`
    - recommended: `trust_policy_url`, `quality_tier`, `tags`

## Validate

Offline repository validation (from this repo):

```bash
scripts/validate-repository.sh
```

From a KelvinClaw release bundle:

```bash
export KELVIN_PLUGIN_INDEX_URL=<index-url>

./kpm install <plugin-id>
./kpm list
```
