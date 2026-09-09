# CLAUDE.md

## What this repository is

Desired-state deployment for one ONCE server on Vultr, created to test the
Vultr compute provider. `colors.yml` is source; `.colors/` is generated and
must never be edited or committed. The desired backend is R2 with compute-require-existing-state enabled.
Retain the former local state until an explicit ownership transfer is complete.
See compute-migration.md before any real operation.

The root `green`, `red`, and `blue` launchers are copies of the corresponding
packages under `.agents/skills/`, installed from `getcolors/once` and recorded
in `skills-lock.json`. After `npx skills update -p -y`, synchronize all three
root launcher copies.

SSH uses the existing disposable `.ssh/id_ed25519` key, registered as
`once-vultr-disposable` with Vultr ID `faa53dae-f289-4bba-bf90-8997131ca40a`.
The desired state now supplies its absolute `ssh-private-key-path` for Ansible.
Update that path if the checkout moves. The package does not regenerate or own
this external key. Its compute library uses cloud-init and Ansible readiness;
OpenTofu remote-exec and its separate agent socket are no longer required.
Existing unmanaged SSH aliases must be reviewed before local alias convergence.

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
