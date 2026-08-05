# CLAUDE.md

Desired state for one Rama node on DigitalOcean. Source behavior is in
`../rama`; this repository contains configuration and an installed launcher
copy. `colors.yml` is the only normal edit. `.colors/` is generated and must
never be read, edited, or committed. Never read `.envrc.private`.

Use `./green build`, `./green create --dry-run`, `./green create`, and
`./green rama ...`. Keep `compute-prevent-destroy: true`; only overlay false for
an explicitly authorized deletion. Never export `COLORS_PAR_PROFILE`.
Credentials are `COLORS_PAR_*` values in ignored `.envrc.private`.

The root `green` is a copy of the Package Skill payload. Re-copy it after every
package update. Do not commit or push without explicit authorization.
