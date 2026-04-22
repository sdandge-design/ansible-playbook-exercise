# Ansible Linux Environment Configuration Playbook

This playbook automates the configuration of a Linux environment on a remote server with the following features:

## What It Does

1. **Creates a bin directory and configures it in PATH**
   - Creates `~/bin` directory
   - Adds `$HOME/bin` to the execution PATH via `.bashrc`

2. **Sets up a Python virtual environment with Ansible**
   - Creates a virtual environment at `~/ansible-venv`
   - Installs Ansible and required collections

3. **Installs Ansible Collections**
   - `kubernetes.core` - For Kubernetes management
   - `community.general` - General community collection

4. **Creates kubectl alias**
   - Adds `alias k=kubectl` to `.bashrc` for convenient kubectl access

## Prerequisites

- Ansible installed on the control node (your local machine)
- SSH access to the remote server with appropriate credentials
- Python 3.8+ on the remote server
- APT package manager (Debian/Ubuntu-based systems)

## Usage

### 1. Configure your inventory

Edit the `inventory` file with your server details:

```ini
[servers]
server1 ansible_host=<server_ip> ansible_user=<username> ansible_ssh_private_key_file=<path_to_key>
```

Example:
```ini
[servers]
server1 ansible_host=172.174.241.98 ansible_user=azureuser ansible_ssh_private_key_file=~/Downloads/key.pem
```

### 2. Run the playbook

```bash
ansible-playbook -i inventory playbook.yml
```

### 3. Verify completion

Use the provided verification commands:

```bash
ssh -i <key_path> <user>@<host> << 'EOF'
echo "=== BIN DIRECTORY CHECK ==="
ls -la ~/bin && echo "✓ bin directory exists"

echo "=== PATH CHECK ==="
echo $PATH | grep -q "$HOME/bin" && echo "✓ bin in PATH"

echo "=== VIRTUAL ENVIRONMENT CHECK ==="
ls -la ~/ansible-venv && echo "✓ venv exists"

echo "=== ANSIBLE CHECK ==="
~/ansible-venv/bin/ansible --version | head -1 && echo "✓ Ansible installed"

echo "=== COLLECTIONS CHECK ==="
~/ansible-venv/bin/ansible-galaxy collection list | grep -E "(kubernetes|community)" && echo "✓ Collections installed"

echo "=== ALIAS CHECK ==="
grep -q "alias k=kubectl" ~/.bashrc && echo "✓ kubectl alias set"
EOF
```

## File Structure

```
ansible-playbook-exercise/
├── playbook.yml          # Main playbook
├── inventory             # Ansible inventory file
├── requirements.yml      # Ansible collections requirements
├── .gitignore           # Git ignore rules
└── README.md            # This file
```

## Notes

- The playbook uses `become: yes` for tasks requiring sudo privileges
- Make sure your SSH key has appropriate permissions (400 or 600)
- The virtual environment can be activated on the remote server with:
  ```bash
  source ~/ansible-venv/bin/activate
  ```
- After running the playbook, start a new shell session for PATH and alias changes to take effect

## Variables

The playbook uses the following variables:

- `user_home` - User's home directory (derived from `ansible_user`)
- `bin_dir` - Binary directory path (`$HOME/bin`)
- `venv_dir` - Virtual environment path (`$HOME/ansible-venv`)

## License

MIT License - Feel free to use and modify as needed
