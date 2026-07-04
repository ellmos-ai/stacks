# The `ellmos-stack-manifest-v1` schema

Every stack in this catalog is described by one manifest file. The manifest **is** the
blueprint — installing a stack means reading the manifest and acting on it (cloning
modules, wiring their `provides`/`consumes` capabilities together), not maintaining a
separate, hand-written install document that can drift from what actually happens.

This keeps every stack self-describing and lets a generic installer (or an AI agent)
set up *any* stack from the same manifest shape, without needing stack-specific
installer code.

## Shape

```json
{
  "schema": "ellmos-stack-manifest-v1",
  "name": "example-stack",
  "description": "One sentence describing what this stack is for.",
  "modules": [
    {
      "name": "module-name",
      "source": { "type": "github", "repo": "org/repo", "ref": "main" },
      "kind": "core | infra | mcp | skills | installer | comm | deploy",
      "enabled": true,
      "boundaries": {
        "net": "none | local | listed",
        "paths": ["./"],
        "tools": []
      },
      "wiring": {
        "provides": ["capability-name"],
        "consumes": ["capability-name"]
      }
    }
  ]
}
```

## Field reference

| Field | Meaning |
|---|---|
| `schema` | Version tag for the manifest shape itself, so tooling can detect breaking changes. |
| `name` | Short stack identifier, matches the repo name by convention. |
| `description` | One sentence, human-readable. |
| `modules[].name` | Short module identifier, used in `wiring` references. |
| `modules[].source` | Where to fetch the module from. Currently `type: "github"` (repo + ref); other source types (`local`, `ollama`, package registries) are used by private/derived manifests but not required here. |
| `modules[].kind` | Rough category, used for grouping/filtering, not for behavior. |
| `modules[].enabled` | Optional, defaults to `true`. `false` marks a module that is planned/documented but not yet installed by the installer. |
| `modules[].boundaries` | Declares what the module is allowed to touch: network reach (`net`), filesystem paths it needs, and any tool-name prefixes it exposes (e.g. an MCP server's tool namespace). Documentation, not sandboxing — the installer does not enforce it. |
| `modules[].wiring.provides` | Capability names this module offers to the rest of the stack. |
| `modules[].wiring.consumes` | Capability names this module expects another module in the same stack to provide. |

## Why `provides` / `consumes` instead of hard dependencies

Capabilities are named abstractly (`"memory"`, `"ticket-routing"`, `"control-plane"`, …)
rather than pointing at a specific module by name. That lets a stack swap one module for
another that offers the same capability (e.g. two different memory backends) without
touching every module that consumes it — the installer/wiring layer resolves
`consumes` against whichever enabled module `provides` it.

## Using this schema for your own stack

1. Pick a `name` and write one `description` sentence.
2. List every module as a `source` reference (a real, publicly cloneable repo — no
   local-only paths in a published manifest).
3. Fill in `wiring` honestly: only declare `provides` capabilities the module actually
   implements, and only declare `consumes` capabilities the module actually needs at
   runtime.
4. Add the stack to the catalog table in the root [`README.md`](../README.md).

See [`ellmos-ai/agent-ops-stack`](https://github.com/ellmos-ai/agent-ops-stack) for a
worked example (`agent-ops.manifest.json` + a small installer that reads it).
