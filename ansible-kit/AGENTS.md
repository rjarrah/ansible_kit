# Agent instructions — ansible-kit

Run Ansible yourself in the project terminal. Do not only paste commands for the user unless they ask for a copy-paste recipe or a command fails because this environment cannot reach their Docker daemon.

## Prefer these entrypoints

From the repo root:

```bash
make status    # container status only
make start     # ensure Docker + hello-world container running, then status
make ping      # inventory connectivity
```

Equivalent playbook forms:

```bash
ansible-playbook playbooks/docker-hello.yml --tags status
ansible-playbook playbooks/docker-hello.yml --tags start
ansible-playbook playbooks/docker-hello.yml
ansible-playbook playbooks/ping.yml
```

## Rules of thumb

- Always `cd` to the repo root (where `ansible.cfg` lives) before running playbooks.
- Install collections once if modules are missing: `ansible-galaxy collection install -r requirements.yml`
- Container name is `hello-world` (image `nginx:alpine`, port `8080`).
- Prefer `make status` / `--tags status` for read-only checks.
- If Docker permission is denied, say the user may need to log out/in after being added to the `docker` group, or run a one-time start with sudo/become.
- This Cloud Agent VM is not the user's laptop. If Docker/state they care about lives on their machine, tell them to open `~/ansible-kit` in a **local** Cursor Agent (Agent mode) so shell commands run there.
