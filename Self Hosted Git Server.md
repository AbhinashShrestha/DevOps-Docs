### Install Git and Set Up Git User

1. **Install Git:**

    ```bash
    sudo dnf install git
    ```

2. **Create Git User:**

    ```bash
    sudo useradd git
    passwd git
    ```

    Set the password to `okaygitb`.

3. **Switch to Git User:**

    ```bash
    su git
    ```

4. **Reference:**

    Follow the [Git Book guide](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server) for detailed setup instructions.

## Git Pull Issue Resolution

When facing issues with `git pull`, use `git fetch` instead:

```bash
git fetch
```

## Clone Repository

To clone a repository from your Git server:

```bash
git clone git@192.168.56.8:/home/git/project1.git
```

## Set Up SSH Tunnel

To access the Git server remotely, set up an SSH tunnel on the host:

```bash
ssh -L 0.0.0.0:8080:192.168.56.8:22 git@192.168.56.8
```

## Clone Repository from Another PC

1. Clone the repository using the SSH tunnel:

    ```bash
    git clone git+ssh://git@local_machine_ip:exposed_port/home/git/project1.git
    ```

2. **Set Up SSH Keys:**

    - Append the RSA public key of the server to `~/.ssh/authorized_keys`:

        ```bash
        cat server_public_key >> ~/.ssh/authorized_keys
        ```

    - Set correct permissions:

        ```bash
        chmod 700 ~/.ssh
        chmod 600 ~/.ssh/authorized_keys
        ```

3. **Fix Password Prompt Issues:**

    Ensure correct permissions on the server:

    ```bash
    sudo chmod 755 /home/anon
    sudo chmod 644 /home/anon/.ssh/*
    ```

## Configure SSH on Development Machine

1. Edit the SSH configuration:

    ```bash
    vi ~/.ssh/config
    ```

2. Add the following configuration:

    ```ini
    Host server_ip
      User git
      IdentityFile ~/.ssh/private_key
    ```

3. Now, you can clone the repository without entering a password.

## Test SSH Connection

To verify the setup, test the SSH connection:

ssh -v git@server_ip
```

### Notes

- Replace `server_ip` with the actual IP address of your Git server.
- Ensure the SSH private key (`~/.ssh/private_key`) is correctly configured on your development machine.
- Make sure the SSH public key of your development machine is added to the `~/.ssh/authorized_keys` on the Git server.