# recurse — TouchDesigner project conventions

## Keep everything externalized to .tox
Every meaningful COMP (a visualization, an effect, a mixer/controller) must be synced to an
external `.tox` file under `components/`, not left living only inside `project.toe`. Use the
COMP's External `.tox` par (Sync to File on) so edits round-trip and git-lfs can track/diff it.
`project.toe` itself should stay a thin shell that instances these `.tox` components and wires
them together.

**Why:** `.toe` is one binary blob — git can't show what changed inside it. Per-component `.tox`
files are what makes commits (see `52d702c`, `c0bb273`) legible: one file per concern, tracked
individually via `*.tox filter=lfs` in `.gitattributes`.

## Keep things modular
- One COMP = one concern. A visualization, an overlay effect, and the mixer/switcher that
  toggles between them are separate COMPs with a clear single TOP (or CHOP) output, not
  merged into one sprawling network.
- New visualizations (beyond the current `video_in` / `text_carousel` / `glow_overlay` /
  `compositor` chain) should each be their own top-level COMP + `.tox`, so a scene-switching
  control flow can treat them as interchangeable inputs (e.g. wired into a `Switch`/`Cross` TOP)
  without reaching inside their internals.
- Externalize non-trivial DAT scripts (Python/GLSL/etc.) to files under `scripts/` rather than
  leaving long inline code only inside the `.toe`, for the same diffability reason.

## Housekeeping
- Numbered auto-backups (`project.1.toe`, etc.) and `Backup/`/`Cache/` are gitignored — never
  commit them.
- Nodes are typically built live in the running instance via the TouchDesigner MCP bridge
  (`mcp__touchdesigner__*` tools), then externalized to `.tox` and committed — prefer that over
  hand-authoring standalone Python network-builder scripts.
