.PHONY: ping start status collections

collections:
	ansible-galaxy collection install -r requirements.yml

ping:
	ansible-playbook playbooks/ping.yml

# Ensures Docker is installed/running and hello-world container is up, then prints status
start: collections
	ansible-playbook playbooks/docker-hello.yml

# Read-only status check for the hello-world container
status:
	ansible-playbook playbooks/docker-hello.yml --tags status
