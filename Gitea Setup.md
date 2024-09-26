1. Prepare MySQL database
```
CREATE USER 'gitea'@'%' IDENTIFIED BY 'gitea';
CREATE DATABASE giteadb CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_bin';
GRANT ALL PRIVILEGES ON giteadb.* TO 'gitea';
FLUSH PRIVILEGES;
```


2. Create a user to run Gitea (e.g. git)
```
groupadd --system git
adduser \
   --system \
   --shell /bin/bash \
   --comment 'Git Version Control' \
   --gid git \
   --home-dir /home/git \
   --create-home \
   git
```

Now cd /home/gitk3s
3. Download binary [link](https://github.com/go-gitea/gitea/releases)
```
wget https://github.com/go-gitea/gitea/releases/download/v1.22.1/gitea-1.22.1-linux-amd64
chmod +x gitea
```
3. Prepare Git Environment
```
git --version
echo "export GITEA_WORK_DIR=/var/lib/gitea/" >> /home/git/.bashrc
```
4. Create required Directories
```
mkdir -p /var/lib/gitea/{custom,data,log}
chown -R git:git /var/lib/gitea/
chmod -R 750 /var/lib/gitea/
mkdir /etc/gitea
chown root:git /etc/gitea
chmod 770 /etc/gitea
```
5. Place the git binary in a global location
```
cp gitea /usr/local/bin/gitea
```
6. LInux service
```
sudo wget -O /etc/systemd/system/gitea.service https://raw.githubusercontent.com/go-gitea/gitea/release/v1.22/contrib/systemd/gitea.service
```
### [Main DOC](https://docs.gitea.com/installation/install-from-binary)  


```
USERNAME = mero@mero.com
PASSWORD = mero@mero.com



admin@admin.com
giteadmin

username mero
password = mero@mero.com
 mero@mero.com
```