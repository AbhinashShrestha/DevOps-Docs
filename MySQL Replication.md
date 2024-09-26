To set up MySQL Master-Slave replication between two servers (Rocky A as Master and Rocky B as Slave), follow these steps:

### On Rocky A (Master)

1. **Install MySQL Server and Secure Installation:**

   ```bash
   sudo dnf install mysql-server
   sudo mysql_secure_installation
   ```

2. **Configure Firewall (Assuming firewall is firewalld):**

   ```bash
   sudo firewall-cmd --permanent --zone=public --add-rich-rule='
     rule family="ipv4"
     source address="192.168.56.9"
     port protocol="tcp" port="3306" accept'
   sudo firewall-cmd --reload
   ```

3. **Edit MySQL Configuration File:**

   ```bash
   sudo vim /etc/my.cnf.d/mysql-server.cnf
   ```

   Ensure the following settings are configured:

   ```
   bind-address = 192.168.56.8
   server-id = 1
   log_bin = mysql-bin
   ```

4. **Log in to MySQL and Create Replication User:**

   ```bash
   mysql -u root -p

   CREATE USER 'slave'@'192.168.56.9' IDENTIFIED BY 'P@ssword321';
   GRANT REPLICATION SLAVE ON *.* TO 'slave'@'192.168.56.9';
   ALTER USER 'slave'@'192.168.56.9' IDENTIFIED WITH mysql_native_password BY 'P^ssword123';
   FLUSH PRIVILEGES;
   ```

5. **Obtain Master Status:**

   ```sql
   SHOW MASTER STATUS \G;
   ```
   
   Note down the `File` and `Position` values. For example:
   ```
   *************************** 1. row ***************************
                File: mysql-bin.000004
            Position: 324
        Binlog_Do_DB:
    Binlog_Ignore_DB:
    Executed_Gtid_Set:
   ```

### On Rocky B (Slave)

1. **Install MySQL Server and Secure Installation:**

   ```bash
   sudo dnf install mysql-server
   sudo mysql_secure_installation
   ```

2. **Edit MySQL Configuration File:**

   ```bash
   sudo vim /etc/my.cnf.d/mysql-server.cnf
   ```

   Configure the following settings:

   ```
   bind-address = 192.168.56.9
   server-id = 2
   log_bin = mysql-bin
   ```

3. **Configure Replication:**

   Stop any existing replication (if applicable):

   ```sql
   STOP REPLICA;
   RESET REPLICA ALL;
   ```

   Configure replication to connect to the master:

   ```sql
STOP REPLICA;
RESET REPLICA ALL;
CHANGE REPLICATION SOURCE TO 
    SOURCE_HOST='192.168.56.8',
    SOURCE_PORT=3306,
    SOURCE_USER='slave',
    SOURCE_LOG_FILE='mysql-bin.000004',
    SOURCE_PASSWORD='P^ssword123',
    SOURCE_LOG_POS=324;
   ```

4. **Start Replication:**

   ```sql
   START REPLICA;
   ```

5. **Verify Replication Status:**

   Check if the Slave_IO_State is "Waiting for master to send event":

   ```sql
   SHOW REPLICA STATUS \G;
   ```

   Look for `Slave_IO_State` in the output to ensure replication is working correctly.

### Notes

- Replace IP addresses (`192.168.56.8` and `192.168.56.9`) with your actual server IPs.
- Ensure firewall rules allow MySQL traffic between the servers.
- Monitor MySQL logs (`/var/log/mysql/error.log`) for any replication errors.
- Ensure that both servers have synchronized time to avoid replication issues.
- Adjust MySQL configuration files (`my.cnf`) based on your specific environment and requirements.
