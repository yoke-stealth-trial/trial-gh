# STEALTH trial gh Yoke Project Contract

This directory is the Yoke project contract for STEALTH trial gh: repo-local
project policy, delivery declarations, and appearance for a Yoke-managed
project. It is not a runtime authority store.

Yoke owns execution truth in its authoritative DB: project capabilities,
provider settings, materialized deployment flows, command definitions, and
event evidence. Project-owned desired configuration in this directory is
materialized into that authority by named commands.

## Files

- `.gitignore` - ignore policy for this tree (generated views and
  machine-local state); root gitignores carry no `.yoke/*` rules.
- `lint-config` - hook guard policy in the line-oriented Yoke format.
- `project.config` - project settings that must be readable with no
  database or network: the authored-file `file_line_limit` and repeated
  `file_line_exception` globs for files exempt from it.
- `labels` - GitHub label color policy in `label_color_*=HEX` format.
- `board-art` - live board header art read by the renderer.
- `packs.json` - repository-authoritative installed-Pack receipt, created by
  Pack get/update operations. It records versions and merge baselines without
  claiming continuing ownership of the resulting project source.
- `test-inventory.md` - project test surfaces and lifecycle placement.
- `runbooks/` - living deploy/recovery docs; people and agents fill them
  in as the project takes shape.
- `strategy/` - rendered strategy-doc views (untracked local renders).
  A separate ownership class: the Yoke DB `strategy_docs` rows are
  authoritative per project, `yoke strategy render` is the only writer,
  edits flow back via `yoke strategy ingest`, refresh overwrites clean
  renders, and uninstall never removes them.

## Where settings live

Repo-owned project files (this directory; rides the repo):

- `project.config` - on-disk project settings that must be readable with no
  database or network: `file_line_limit` and repeated `file_line_exception`
  globs for the local hook and `yoke check file-line`.
- `lint-config` - hook guard modes (`<guard>=deny|warn`).
- `labels` - GitHub label colors (`label_color_*=HEX`).
- `packs.json` - installed Pack versions and immutable merge baselines. Pack
  output is ordinary project-owned source; customization is expected and is
  not classified as drift.
- `strategy/` - untracked rendered strategy-doc views (DB-authoritative;
  edit via `yoke strategy ingest`).

DB-owned execution truth and project policy:

- Deployment flows (`deployment_flows`): define one with
  `yoke deployment-flows create <flow-id> --project <project> --name NAME
  --stages-file PATH`, retire one with
  `yoke deployment-flows set-status <flow-id> disabled`, and read the
  project default with `yoke project-structure deploy-defaults get`.

- Project row (slug, prefix, breakage policy, repo path):
  `yoke projects get|update --project <project>`.
- Project policy (`project_capabilities.type='project-policy'`): shared
  scalar behavior such as `base_branch`, `wip_cap`, `default_priority`,
  `merge_conflict_threshold`, and `max_attempts`, plus nested `board`
  appearance/scope settings. The authored-file line limit is not here — it is
  checked-in policy in `.yoke/project.config` so the offline pre-commit
  hook can enforce it in a fresh clone.
- Session routing (`project_capabilities.type='session-routing'`): shared
  lane defaults, harness/model selectors, and presentation metadata.
- Capabilities (`project_capabilities` + `capability_secrets`):
  `yoke projects capability has` and
  `yoke projects capability-secret set`.
- Environments and sites (`environments.settings`, `sites.settings`): model
  desired settings through Project Structure patches or the supported
  project-onboarding surfaces until a dedicated product command exists.
- Project structure families (deployment default, context routing,
  architecture model):
  `yoke project-structure deploy-defaults get` and
  `yoke project-structure patch apply`. Deterministic project commands are
  Command-method QA plans, read through `yoke qa plan list|get`.

The generated board view always lands at `.yoke/BOARD.md` — never edit it,
and it is never an installed contract file. Board scope and appearance are
DB-owned under `project-policy.settings.board`.

These files are seeded once by `yoke project install` and never
overwritten on refresh; project edits are preserved. Delete a file and
`yoke project refresh` recreates it at current defaults.

Do not add credentials, active environment bindings, local databases, backup
directories, scratch/session directories, QA capture folders, or generated
runtime trees under this contract.
