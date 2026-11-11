# Ansible starter

Small Ansible layout for managing a laptop (`localhost`) and a few remote hosts.

## Layout

```
ansible.cfg              # defaults (inventory, roles path, SSH)
inventory/
  hosts.yml              # hosts and groups
  group_vars/
    all.yml              # vars for every host
    web.yml              # vars for the web group
playbooks/
  ping.yml               # connectivity check (no sudo)
  site.yml               # apply the common role everywhere
  bootstrap.yml          # first-run setup for web/db hosts
  docker-hello.yml       # install/start Docker, run hello-world
roles/
  common/                # packages + MOTD baseline
  docker_hello/          # Docker engine + hello-world container
requirements.yml         # Galaxy collections (includes community.docker)
```

## Prerequisites

Install Ansible on the machine where you run playbooks (the control node):

```bash
# Debian / Ubuntu
sudo apt update && sudo apt install -y ansible

# Fedora
sudo dnf install -y ansible

# macOS (Homebrew)
brew install ansible

# Any OS with pipx (isolated, up-to-date)
pipx install --include-deps ansible
```

Confirm it works:

```bash
ansible --version
```

You also need SSH access to remote hosts (or use `localhost` with the local connection already defined in inventory).

Install collections (needed for the Docker playbook):

```bash
ansible-galaxy collection install -r requirements.yml
```

## Quick start

1. Edit `inventory/hosts.yml` — uncomment and fill in your hosts, or keep `localhost`.
2. Smoke-test:

```bash
ansible-playbook playbooks/ping.yml
```

3. Apply the common baseline (needs sudo on the target):

```bash
ansible-playbook playbooks/site.yml --ask-become-pass
```

4. Start Docker and keep a container named `hello-world` running (nginx:alpine on port 8080):

```bash
ansible-galaxy collection install -r requirements.yml
ansible-playbook playbooks/docker-hello.yml
# First-time Docker install only:
# ansible-playbook playbooks/docker-hello.yml --ask-become-pass
```

Or use Make (preferred for Cursor Agent):

```bash
make start     # ensure container is running, then show status
make status    # status only
make ping
```

Check status only (does not recreate the container):

```bash
ansible-playbook playbooks/docker-hello.yml --tags status
```

Start only (skip the status print):

```bash
ansible-playbook playbooks/docker-hello.yml --tags start
```

### Letting Cursor run Ansible for you

This repo includes `AGENTS.md` and `.cursor/` rules so a **local** Cursor Agent will execute `make` / `ansible-playbook` itself instead of only suggesting commands.

1. Open `~/ansible-kit` in Cursor Desktop.
2. Use **Agent** mode (not Ask).
3. Under **Settings → Agents → Approvals & Execution**, keep Auto-review (or allow `ansible-playbook` / `make`), and approve Docker/shell prompts when asked.
4. Ask things like: “check hello-world status” or “start the docker container”.

A Cloud Agent runs on a remote VM, so it cannot see Docker on your laptop. Use a local Agent for live container control.

Limit to one group or host:

```bash
ansible-playbook playbooks/site.yml -l web --ask-become-pass
ansible-playbook playbooks/ping.yml -l localhost
```

## Adding a host

In `inventory/hosts.yml`:

```yaml
web:
  hosts:
    web1.example.com:
      ansible_host: 192.168.1.10
      ansible_user: deploy
```

Put SSH keys or `ansible_password` / vaulted secrets in place before running playbooks against it.

## Adding a role

```bash
ansible-galaxy role init roles/myapp
```

Then reference it from a playbook:

```yaml
roles:
  - role: myapp
```
