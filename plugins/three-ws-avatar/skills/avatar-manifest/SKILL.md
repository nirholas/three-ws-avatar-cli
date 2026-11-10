---
name: avatar-manifest
description: Scaffold, validate, hash and preview three.ws on-chain avatar manifests with the three-ws-avatar CLI. Use when a user has a GLB, GLTF or VRM mesh and a wallet address and needs a spec-compliant manifest, or needs to check one in CI.
---

# Avatar manifests with @three-ws/avatar-cli

The binary is `three-ws-avatar`. Every command runs offline against `@three-ws/avatar-schema`, with no account and no browser. Run with `npx @three-ws/avatar-cli <command>` or install globally with `npm install -g @three-ws/avatar-cli`.

## Commands

```bash
three-ws-avatar init --owner <caip10|0xaddr> --name <string> --mesh <path> \
  [--skeleton avaturn|mixamo|rpm|vrm-humanoid|custom] [--mesh-uri <https:// or ipfs://>] \
  [--id <override>] [--out manifest.json]
three-ws-avatar validate <path> [--json]   # exit 0 valid, exit 1 invalid with each error path
three-ws-avatar hash <path> [--json]       # SHA-256 lowercase hex of any file
three-ws-avatar preview <path>             # resolver URL, <agent-3d> snippet with loader, <iframe> snippet
three-ws-avatar --help | --version
```

Details:

- `--owner` takes a full CAIP-10 (`eip155:1:0x...`) or a bare `0x...` address (assumed `eip155:1`).
- `--mesh` accepts `.glb`, `.gltf`, `.vrm`. SHA-256, byte size and format are computed for you.
- `--skeleton` defaults to `avaturn`.
- `--mesh-uri` replaces the local `file://` path with a public URI such as an IPFS or S3 URL. Set it before publishing.
- The id defaults to one derived from the owner's CAIP-10, or from `--name` when it is an ENS-style `*.eth`, `*.ws` or `*.sol` name.
- `init` validates against the schema before writing, so any output it produces is valid.

## Worked example

```bash
curl -sLO https://three.ws/avatars/michelle.glb
three-ws-avatar init --owner eip155:1:0x742d35Cc6634C0532925a3b844Bc454e4438f44e \
  --name "Nicholas" --mesh ./michelle.glb --skeleton mixamo --out manifest.json
three-ws-avatar validate manifest.json
three-ws-avatar preview manifest.json
```

The preview prints the resolver URL `https://three.ws/a/{id}` plus the embed snippets. Both the `<agent-3d>` element and its loader script are required for the embed to work.

## CI usage

Gate pull requests with `three-ws-avatar validate manifest.json --json` (non-zero exit on failure), and pin a mesh with `three-ws-avatar hash ./model.glb` to compare against the manifest's recorded sha256.
