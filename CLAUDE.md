# CLAUDE.md

## What this repository is

Desired state for `rama-digitalocean`: one private single-node Rama cluster on a
DigitalOcean Droplet in Amsterdam. ZooKeeper, Rama Conductor, and one Supervisor
run on the host; WireGuard is the service boundary. Cloudflare publishes a
DNS-only endpoint and Resend provides machine mail.

This repository contains configuration and an installed Package Skill, not Rama
package source. Behavior lives in `../rama`.

```text
colors.yml                         non-secret desired state; the normal edit
green                              installed launcher, copied from the payload
.agents/skills/package-rama-green  installed Package Skill
skills-lock.json                   installed source and content hash
.envrc                             secret-free environment loader
.envrc.private                     ignored credentials; never read or commit
.colors/                           generated private state; never read or commit
index.html                         public deployment runbook
.nojekyll                          GitHub Pages marker
```

The default-deny `.gitignore` hides dotfiles unless explicitly negated. Check
`git ls-files` instead of inferring what is tracked from the working tree.

## Commands

```sh
./green build                    # render only; no provider calls
./green create --dry-run         # print the workflow; no side effects
./green create                   # provision and converge; authorization required
./green rama conductorReady      # local CLI through WireGuard
./green rama numSupervisors
./green delete                   # guarded, destructive, authorization required
```

Build and dry-run work without credentials. Never run real create/delete without
explicit authorization.

## Live desired state

The committed deployment pins Rama 1.9.0, ZooKeeper 3.8.4, and Java 21 on one
DigitalOcean `s-8vcpu-16gb` Ubuntu 24.04 Droplet in `ams3`. The deployment owns
its VPC. SSH is restricted to the committed operator CIDR; WireGuard UDP accepts
roaming clients. Rama and ZooKeeper ports must remain private.

Cloudflare creates an unproxied record for the Rama host. Resend creates and
verifies the notification domain and configures SMTP relay. `rama-license` is
false; if licensing is enabled later, only the local source path may be supplied
through `COLORS_PAR_RAMA_LICENSE_SOURCE_PATH`.

The desired backend is R2 with compute-require-existing-state enabled.
Retain the former local `.colors/` state until the ownership transfer described
in compute-migration.md is complete. It also contains generated WireGuard client
material and is sensitive. Never use it as source, publish it, edit it, or commit
it.

## Credentials and guards

Provider credentials are `COLORS_PAR_*` values in ignored `.envrc.private`.
Never place values in `colors.yml`, documentation, generated examples, logs, or
chat. Never export `COLORS_PAR_PROFILE`; changing it can select another work tree
or state and the launcher must refuse it.

Keep `compute-prevent-destroy: true` committed. For one explicitly authorized
delete only, overlay it with:

```sh
COLORS_PAR_COMPUTE_PREVENT_DESTROY=false ./green delete
```

Do not edit the YAML guard to make deletion possible.

## Installed launcher

The root `green` is a copy of
`.agents/skills/package-rama-green/green`, not a symlink. After every package
update, re-copy it or the root launcher keeps the old pin while the lockfile
claims the new payload:

```sh
npx skills update -p -y
cp .agents/skills/package-rama-green/green green
```

Do not hand-edit the stamped SHA. For local package development, use
`RAMA_LIB_ROOT=../rama ./green build` deliberately rather than changing the pin.

## Verification

After an authorized create, the minimum package-level checks are:

```sh
./green rama conductorReady
./green rama numSupervisors
```

If the CLI cannot connect, verify the WireGuard tunnel and route before changing
firewall or Rama service settings. Publicly exposing a service port is not a
recovery step.

## Documentation and GitHub Pages

`index.html` is the public deployment runbook and `.nojekyll` keeps GitHub Pages
from applying Jekyll processing. The page carries two analytics tags: GA4 ID
`G-4VKP1WY4QJ`, whose explicit `page_title` must exactly match the decoded
`<title>`, and the self-hosted Rybbit snippet
`<script src="https://rybbit.getcolors.ai/api/script.js" data-site-id="9fb9c41a6d49" defer></script>`,
which shares one site ID across every repository page because
`getcolors.github.io/<repo>/` paths already encode the repository. Keep both
tags together. Do not publish private addresses, credentials, license content,
or generated state.

## Git

Work on the current branch. Do not commit or push unless explicitly authorized.
