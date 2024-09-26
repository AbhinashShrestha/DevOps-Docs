## Install Ansible

```bash
sudo dnf install ansible
```

## Configure Ansible Hosts

Edit the Ansible hosts file to add your target host:

```bash
sudo vim /etc/ansible/hosts
```

Add the following line to define your host:

```ini
host1 ansible_ssh_host=203.0.113.111
```

## Test Connection to Host

Use the `ping` module to test the connection to your host:

```bash
ansible -m ping host1
```

### Notes

- Ensure the target host (203.0.113.111) is reachable and accessible via SSH.
- You might need to configure SSH keys or provide SSH credentials for the Ansible connection.
- The default location for the hosts file is `/etc/ansible/hosts`, but you can specify a different inventory file using the `-i` option with Ansible commands if needed.