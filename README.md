# Windows Automation Workshop Template (Ansible Navigator)

Get started quickly with Windows automation using Ansible Navigator and the Ansible Automation Platform 2.5 execution environment.

## What’s included
- `ansible-navigator.yml` – preconfigured to use the `registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel9:latest` EE and mount a `.vault-password` from this working directory into the container
- `inventory` – example `windows` group with two hosts
- `group_vars/windows.yml` – WinRM connection variables; username defaults to `ansible`, password is pulled from vault
- `vault.yml` – encrypted Ansible Vault file (contains `vault_win_password`)
- `test_windows_connectivity.yml` – simple playbook to verify WinRM connectivity and identity

## Prerequisites
- Podman installed and working
- The execution environment image should already be present locally (pre-downloaded):
  - Verify: `podman images | grep ee-supported-rhel9`
- Ask your instructor for the vault password

## 1) Create the vault password file
The Navigator config mounts a `.vault-password` from this repository’s root (your current working directory) and sets `ANSIBLE_VAULT_PASSWORD_FILE` automatically.

```bash
echo 'PASTE_INSTRUCTOR_PASSWORD_HERE' > ./.vault-password
chmod 600 ./.vault-password
```

Note: `.vault-password` is already in `.gitignore` and will not be committed. To use a different path, change the `volume-mounts.src` in `ansible-navigator.yml`.

## 2) Configure your Windows hosts
Edit `inventory` and set the correct IPs/hostnames under the `windows` group. Adjust `group_vars/windows.yml` if you need a different username or WinRM settings. The password is sourced from `vault.yml` via `vault_win_password`.

Ensure your Windows hosts are reachable over WinRM HTTP (port 5985) from your machine.

## 3) Test connectivity
Run the included test playbook using Ansible Navigator (uses the EE container and your mounted vault password automatically). This command injects the vaulted vars file without using `vars_files`:

```bash
ansible-navigator run test_windows_connectivity.yml -i inventory -e @vault.yml -m stdout
```

You should see a successful `win_ping` and a debug message like "Connected as: ...".

## Troubleshooting (quick)
- Vault password issues: confirm the file exists at `./.vault-password` and has correct contents/permissions
- WinRM connection errors: verify network reachability to port 5985 and credentials
- Authentication failures: confirm `ansible_user` in `group_vars/windows.yml` and the `vault.yml` password