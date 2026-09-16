# terrible-playbook

A deliberately terrible Ansible playbook designed to trigger every possible
[APME](https://github.com/ansible/apme) validation rule. This repo exists
for demo and testing purposes — **do not use this as a reference for writing
Ansible content.**

## What's wrong with it?

Everything. This playbook violates ~50 lint, modernization, risk, policy,
and secrets-detection rules simultaneously, including:

- Short module names instead of FQCN
- `shell` where `command` suffices, `command` where a module exists
- Missing `changed_when`, `mode`, `name`, `no_log`
- Hardcoded secrets (API keys, passwords, private keys)
- Deprecated bare `include`, `with_items`, Python 2 interpreter
- Parameterized command execution, downloads from untrusted sources
- Argspec violations on `community.general` modules
- Tabs in YAML, bad Jinja spacing, implicit state
- A role with no `meta/main.yml`

## Usage

```bash
apme scan /path/to/terrible-playbook
```

### No-Galaxy mode (portal / avoid ~16% collection install)

This clone is configured for **Nilashish-style** demos: no `requirements.yml`
collections, no collection FQCNs in scanned YAML. Scans should not call
`ansible-galaxy` via galaxy-proxy.

- Scanned content: `site.yml`, `playbook-l040-tabs.yml`, roles, etc.
- **Not scanned:** `archives/` (collection-backed examples preserved there)

```bash
# run-cli.sh only mounts the apme/ directory — target must live inside it
# (a host symlink to ../terrible-playbook does not resolve in the pod).
cp -R terrible-playbook apme/terrible-playbook
cd apme && bash containers/podman/run-cli.sh check -v terrible-playbook
```

To test collection installs again, restore tasks from
`archives/collection-tasks-removed-from-site.yml` into `site.yml` and add
collections to `requirements.yml`.
