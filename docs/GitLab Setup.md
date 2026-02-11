VM Name = gitlab

1. Prerequisite
```
sudo dnf install -y curl policycoreutils openssh-server perl
```
2. Add repo
```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
```
3. Install
```
sudo EXTERNAL_URL="https://mero.gitlab.com" dnf install -y gitlab-ee
```
4. Reconfigure
```
sudo vim /etc/gitlab/gitlab.rb
sudo vim /etc/gitlab/initial_root_password 
sudo gitlab-ctl reconfigure
sudo systemctl status  gitlab-runsvdir

```

```
Administrator 
username = root
password = MeroAfnai#1

Normal User
email = merodev@abcd.com
username = merodev
password = Mer&982jjs
```

```
personal access token
glpat-zLDLqs28PnkqE5ffZy1j
```