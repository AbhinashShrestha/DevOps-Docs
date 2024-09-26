### SSH Tunneling Reference for DevOps

#### Setting Up SSH Tunneling

To establish an SSH tunnel from your local machine to a remote server:

```bash
ssh -L 8080:target_domain:80 username@server_IP
```

Example:
```bash
ssh -L 8080:localhost:80 anon@192.168.56.5
```

#### Accessing Services via SSH Tunnel

Access a service running on a remote server through the SSH tunnel:

```bash
ssh -L 0.0.0.0:8080:192.168.56.8:22 git@192.168.56.8
```

#### Cloning Git Repository Over SSH Tunnel

Clone a Git repository using SSH tunneling:

```bash
git clone git+ssh://git@local_machine_ip:exposed_port/home/git/project1.git
```

#### SSH Configuration for Seamless Access

Configure SSH for seamless access to the server:

1. Edit SSH configuration:
   ```bash
   vi ~/.ssh/config
   ```
2. Add the following configuration:
   ```
   Host server_ip
     User git
     IdentityFile ~/.ssh/private_key
   ```

3. Append the RSA public key to `~/.ssh/authorized_keys` on the server.

#### Verification and Testing

Test SSH connection verbosity for troubleshooting:

```bash
ssh -v git@server_ip
```

### Notes

- Replace `server_IP`, `target_domain`, `username`, `local_machine_ip`, `exposed_port`, and other placeholders with your actual server and network details.
- Ensure SSH keys are properly configured and permissions are set correctly (`chmod 600 ~/.ssh/private_key`).
- Adjust tunneling ports (`8080`, `80`, `22`) and configurations based on your specific use case and security requirements.