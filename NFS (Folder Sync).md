### On Master (Rocky A)

1. Install NFS utilities and start services:
   ```bash
   sudo yum install -y nfs-utils
   systemctl start nfs-server rpcbind
   systemctl enable nfs-server rpcbind
   ```

2. Create a directory to share:
   ```bash
   sudo mkdir /mero_nfs_matra
   sudo chmod 777 /mero_nfs_matra/
   ```

3. Configure NFS export:
   Add the following line to `/etc/exports`:
   ```bash
   echo "/mero_nfs_matra 192.168.56.9(rw,sync,no_root_squash)" | sudo tee -a /etc/exports
   ```

4. Update firewall rules to allow NFS:
   ```bash
   firewall-cmd --permanent --add-service mountd
   firewall-cmd --permanent --add-service rpc-bind
   firewall-cmd --permanent --add-service nfs
   firewall-cmd --reload
   ```

### On Client (Rocky B)

1. Install NFS utilities:
   ```bash
   sudo yum install -y nfs-utils
   ```

2. Check NFS exports on the server:
   ```bash
   showmount -e 192.168.56.8
   ```

3. Create a mount point on the client:
   ```bash
   sudo mkdir /mnt/mero_nfs_matra
   ```

4. Mount the NFS share from the server:
   ```bash
   sudo mount 192.168.56.8:/mero_nfs_matra /mnt/mero_nfs_matra
   ```

5. Add the NFS mount to `/etc/fstab` to make it persistent across reboots:
   ```bash
   echo "192.168.56.8:/mero_nfs_matra /mnt/mero_nfs_matra nfs defaults 0 0" | sudo tee -a /etc/fstab
   ```

### Notes:
- Adjust `/etc/exports` on Rocky A to specify the correct IP of Rocky B and the directory to export.
- Ensure firewall rules allow NFS traffic on both systems.
- Replace `192.168.56.8` with the actual IP address of Rocky A and `192.168.56.9` with the IP address of Rocky B.
- Modify permissions (`chmod`) and other settings as needed based on security requirements.

### For more reference
-[https://dev.to/prajwalmithun/setup-nfs-server-client-in-linux-and-unix-27id](https://dev.to/prajwalmithun/setup-nfs-server-client-in-linux-and-unix-27id)

-[https://datatracker.ietf.org/doc/html/rfc7530](https://datatracker.ietf.org/doc/html/rfc7530)