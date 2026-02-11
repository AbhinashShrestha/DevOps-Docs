oTo set up Gogs (Go Git Service) with MariaDB on Rocky Linux 9.4, follow these steps. Gogs is a self-hosted Git service similar to GitHub or GitLab but designed to be lightweight and easy to set up.

### Step 1: Install MariaDB Server

1. **Add MariaDB Repository:**

   Create a new file named `MariaDB.repo` in `/etc/yum.repos.d/` and paste the following configuration:

   ```plaintext
   [mariadb]
   name = MariaDB
   baseurl = https://downloads.mariadb.org/mariadb/repositories/#distro#/#version#/#arch#/
   gpgkey = https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
   gpgcheck=1
   ```

2. **Install MariaDB Server:**

   ```bash
   sudo dnf install MariaDB-server
   ```

3. **Secure MariaDB Installation:**

   ```bash
   sudo mysql_secure_installation
   ```

   Follow the prompts to set up a secure MariaDB installation.

### Step 2: Configure MariaDB for Gogs

1. **Run MariaDB Commands:**

   Log into the MariaDB server as root:

   ```bash
   mysql -u root -p
   ```

   Enter your root password when prompted.

2. **Set MariaDB Configuration (Optional):**

   Depending on your MariaDB version and configuration needs, adjust the database settings. For example:

   ```sql
   SET GLOBAL innodb_file_per_table = ON,
       innodb_file_format = Barracuda,
       innodb_large_prefix = ON;
   ```

   Execute the SQL statements suitable for your environment.

3. **Create Gogs Database:**

   Create a database and user for Gogs:

   ```sql
   CREATE DATABASE IF NOT EXISTS gogs CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
   GRANT ALL PRIVILEGES ON gogs.* TO 'gogs'@'localhost' IDENTIFIED BY 'P@sswor1';
   FLUSH PRIVILEGES;
   ```

   Adjust `'P@sswor1'` to your desired password.

### Step 3: Install and Configure Gogs

1. **Install Gogs:**

   Follow the comprehensive guide provided by Shape.host or TechRepublic to install Gogs on Rocky Linux 8.

2. **Configure Gogs:**

   During the Gogs setup, configure it to use the MariaDB database (`gogs`), and provide the credentials (`gogs` user and password) created earlier.

### Step 4: Manage Repositories in Gogs

1. **Clone Existing Repository:**

   To clone an existing repository into Gogs:

   ```bash
   git clone git@192.168.56.8:/home/git/gogs-repositories/rocky_desu/java_app.git
   ```

   Replace `192.168.56.8` and `/home/git/gogs-repositories/rocky_desu/java_app.git` with your server IP and repository path.

2. **Add Existing Repository as Remote:**

   To add an existing repository as a remote in Gogs:

   ```bash
   git remote add origin git@192.168.56.8:/home/git/gogs-repositories/rocky/choco.git
   ```

   Adjust `192.168.56.8` and `/home/git/gogs-repositories/rocky/choco.git` accordingly.

3. **Database Management (Optional):**

   If you encounter issues related to the `is_bare` column in the `repository` table in MariaDB, update it as needed:

   ```sql
   USE gogs;
   UPDATE repository SET is_bare = 0 WHERE id = 1;
   ```

   Replace `1` with the appropriate repository ID.

By following these steps, you can successfully set up Gogs with MariaDB on Rocky Linux 8, allowing you to host your own local Git repository effectively. Adjust configurations and paths as per your specific environment and requirements.