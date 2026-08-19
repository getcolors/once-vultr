# CLAUDE.md

## What this repository is

Desired-state deployment for one ONCE server on Vultr, created to test the
Vultr compute provider. `colors.yml` is source; `.colors/` is generated and
must never be edited or committed. The OpenTofu backend is local, so generated
state must not be deleted while the deployment exists.

The root `green`, `red`, and `blue` launchers are copies of the corresponding
packages under `.agents/skills/`, installed from `getcolors/once` and recorded
in `skills-lock.json`. After `npx skills update -p -y`, synchronize all three
root launcher copies.

SSH uses a disposable key, the untracked `.ssh/id_ed25519`
("once-vultr-disposable", uploaded to Vultr id
`faa53dae-f289-4bba-bf90-8997131ca40a`). This machine's `~/.ssh/config`
disables agents (`IdentitiesOnly yes`, `IdentityAgent none`), so the key
reaches OpenSSH explicitly on three paths: `.envrc` exports
`ANSIBLE_SSH_ARGS` naming it for the Ansible stages; a `Host once-vultr
209.250.253.1` block in `~/.ssh/config` names it for `describe` and
interactive ssh (remove that block with the deployment); and the agent on
`.ssh/agent.sock` (see `.envrc.private`) serves only OpenTofu's `remote-exec`,
whose Go SSH client ignores `~/.ssh/config`. If the agent is not running,
restart it with `ssh-agent -a .ssh/agent.sock` and `ssh-add .ssh/id_ed25519`.

## Commands

```sh
./green build
./green create --dry-run
./green create
./green delete
```

Build and dry-run require no credentials. Real provider operations use the
`COLORS_PAR_*` secrets loaded from the ignored `.envrc.private`. Never export
`COLORS_PAR_PROFILE`.

Keep `compute-prevent-destroy: true`. Lift it only for one authorized delete
with `COLORS_PAR_COMPUTE_PREVENT_DESTROY=false`; never run a real create or
delete without explicit authorization.

## Git

Do not commit or push unless explicitly asked.
