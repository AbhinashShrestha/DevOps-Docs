### Installing Jenkins

1. Add the Jenkins repository:
    ```sh
    sudo wget -O /etc/yum.repos.d/jenkins.repo \
        https://pkg.jenkins.io/redhat-stable/jenkins.repo
    sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
    ```

2. Upgrade the system:
    ```sh
    sudo yum upgrade
    ```

3. Install required dependencies and Jenkins:
    ```sh
    sudo yum install fontconfig java-17-openjdk
    sudo yum install jenkins
    ```

4. Reload systemd manager configuration:
    ```sh
    sudo systemctl daemon-reload
    ```

5. Enable and start Jenkins:
    ```sh
    sudo systemctl enable jenkins
    sudo systemctl start jenkins
    ```

6. Check the status of Jenkins:
    ```sh
    sudo systemctl status jenkins
    ```
7. Set the custom JAVA path 
```/usr/lib/systemd/system/jenkins.service

Environment="JAVA_HOME=/usr/lib/jvm/java-17-openjdk-17.0.11.0.9-2.el9.x86_64"
```
## Configuring Firewall for Jenkins

1. Set the port for Jenkins:
    ```sh
    YOURPORT=8080
    PERM="--permanent"
    SERV="$PERM --service=jenkins"
    ```

2. Create a new service for Jenkins in the firewall:
    ```sh
    firewall-cmd --permanent --new-service=jenkins
    sudo firewall-cmd --permanent --service=jenkins --set-short="Jenkins ports"
    firewall-cmd --permanent --service=jenkins --set-description="Jenkins port exceptions"
    firewall-cmd --permanent --service=jenkins --add-port=8080/tcp
    firewall-cmd --permanent --add-service=jenkins
    firewall-cmd --zone=public --add-service=http --permanent
    firewall-cmd --reload
    ```

## Web UI Setup

1. Retrieve the initial admin password:
    ```sh
    cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

2. Use the following credentials:
    ```
    Password = 439e0205b03144fdb51cbbc90b96c044
    ```

3. Build your project and proceed with further configuration as needed.![[Jenkiins Docker UI.png]]


Script
```
sudo dnf install java-17-openjdk-devel
sudo dnf install fontconfig
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo dnf upgrade
sudo dnf install jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins
```


user
admin@admin.com

Admin Account
username = CIadmin
password = forCIDevOps