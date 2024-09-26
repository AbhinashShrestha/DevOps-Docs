wget https://github.com/wso2/product-apim/releases/download/v4.3.0/wso2am-4.3.0.zip
unzip  https://github.com/wso2/product-apim/releases/download/v4.3.0/wso2am-4.3.0.zip
cd wso2am/bin


Now we need java 17 
sudo yum install java-17-openjdk-devel
put the path 

vim ~/.bashrc
```
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-17.0.11.0.9-2.el9.x86_64 export PATH=$JAVA_HOME/bin:$PATH
```
```
bash api-manager.sh 
```

To stop 
```
bash api-manager.sh stop
```

go to https://<server_ip>:9443/publisher
or /carbon
username = admin
password = admin


https://apim.docs.wso2.com/en/latest/troubleshooting/troubleshooting-invalid-callback-error/
## Change all localhost stuff in /config/deployment.toml to server_ip and carbon.xml


## In service list in carbon dash board change the outh and inbound routes
