# Changelog

## Unreleased

- Named cross-machine sync in the `agent-ops-stack` catalog entry (README EN/DE,
  `llms.txt`). That stack gained `sync-master` as a seventh module on 2026-07-23;
  the catalog still described the six-module capability set.
- Clarified the canonical `ellmos-ai/stacks` search context and its distinction from
  the Stacks Bitcoin blockchain, generic cloud stacks, and hosted LLM platforms.
- Refreshed crawler context and banner accessibility metadata.

## 1.0.1 (2026-07-13)

- Clarified that `agent-ops-stack` is an independent stack family, while
  `ellmos-stack` and the planned research/dev/media stacks share the original
  stack-family lineage.
- Added the public `modules[].kind` labels to the manifest schema reference and
  refreshed `llms.txt`.

## 1.0.0 (2026-07-04)

- Initial release: stack catalog (`ellmos-stack`, `agent-ops-stack`, plus planned
  `ellmos-research-stack` / `ellmos-dev-stack` / `ellmos-media-stack`), the
  `ellmos-stack-manifest-v1` schema reference (`docs/manifest-schema.md`), and the
  "what is a stack" composition-principle documentation.
