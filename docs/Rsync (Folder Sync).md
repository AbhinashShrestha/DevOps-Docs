1. Check SSH service status:
   ```bash
   sudo systemctl status sshd
   ```

2. Generate SSH key pair (if not already done):
   ```bash
   ssh-keygen
   ```

3. Start the SSH agent and add the SSH private key:
   ```bash
   ssh-agent bash
   ssh-add ~/.ssh/id_rsa
   ```

4. Copy the SSH public key to the remote server:
   ```bash
   ssh-copy-id remote_username@remote_server_ip_address
   ```

   Now you can log in to the remote server without a password (not as root).

### Sync Files Using Rsync

#### Pull (Get new files from remote)

```bash
rsync -auhvrPt --delete anon@192.168.56.8:/home/anon/this/ /home/anon/this
```

#### Push (Send new files to remote)

```bash
rsync -auhvrPtO --no-group --delete --update /home/anon/this/ anon@192.168.56.8:/home/anon/this
```

#### Sync Both Ways

```bash
rsync -auhvrPtO --no-group --delete --update anon@192.168.56.8:/home/anon/this/ /home/anon/this
rsync -auhvrPtO --no-group --delete --update /home/anon/this/ anon@192.168.56.8:/home/anon/this
```

### Create Daemon Script (No Cron Needed)

1. Edit the crontab: (if scheduled)
   ```bash
   crontab -e
   ```

2. Add the following line to run the script at reboot:
   ```
   @reboot /home/anon/filesync.sh
   ```

3. Create `filesync.sh` script:

   ```bash
   #!/bin/bash

   while true; do
       rsync -auhvrPtO --no-group --delete --update anon@192.168.56.8:/home/anon/this/ /home/anon/this
       rsync -auhvrPtO --no-group --delete --update /home/anon/this/ anon@192.168.56.8:/home/anon/this
       sleep 20
   done
   ```

### Creating a Daemon

To run the script as a daemon, refer to this guide: [How to create a daemon script](https://www.baeldung.com/linux/bash-daemon-script).