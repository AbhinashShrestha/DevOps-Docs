### Configuring Nginx as a Reverse Proxy for Apache Tomcat

#### 1. Change the Proxy Name in `server.xml` (Tomcat)

- Locate the `server.xml` file in your Apache Tomcat configuration directory.
- Modify the `<Connector>` element to include the `proxyName` attribute.

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"
           proxyName="your-desired-proxy-name"/>
```

#### 2. Create Nginx Configuration File

- Navigate to the Nginx configuration directory:
```bash
cd /etc/nginx/conf.d/
```

- Create a new configuration file named `my-config.conf`:
```bash
sudo nano my-config.conf
```

#### 3. Configure the Nginx Server Section

- Inside `my-config.conf`, set up the server block to proxy requests to Tomcat.

```nginx
server {
    listen 80;
    server_name your-desired-proxy-name;

    location / {
        proxy_pass http://tomcat_server_ip:tomcat_port;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
- Replace `your-desired-proxy-name` with the desired domain or subdomain for your app.
- Replace `tomcat_server_ip:tomcat_port` with the IP address and port where your Tomcat server is running.

#### 4. Test and Restart Nginx

- Test the Nginx configuration for syntax errors:
```bash
sudo nginx -t
```

- Restart Nginx to apply the new configuration:
```bash
sudo systemctl restart nginx
nginx -s reload
```

- Check the status of Nginx to ensure it is running properly:
```bash
sudo systemctl status nginx
```

Test if any service is using 
```
sudo netstat -tulnp | grep :80
sudo ss -tlnp | grep ':80'
```


Disable selinux 
```
sudo setenforce 0
```
