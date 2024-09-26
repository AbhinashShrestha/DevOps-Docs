#### 1. Create the Directory for Your Website
```bash
sudo mkdir -p /var/www/mero-afnai-devops.com/html
```

#### 2. Change Ownership of the Directory
```bash
sudo chown -R $USER:$USER /var/www/mero-afnai-devops.com/html
```

#### 3. Set Permissions for the Directory
```bash
sudo chmod -R 755 /var/www/mero-afnai-devops.com/html
```

#### 4. Create Your Website's Index File
```bash
sudo nano /var/www/mero-afnai-devops.com/html/index.html
```

#### 5. Configure Nginx for Your Website
```bash
sudo nano /etc/nginx/conf.d/mero-afnai-devops.com.conf
```

#### 6. Verify the Nginx Configuration
```bash
sudo cat /etc/nginx/conf.d/mero-afnai-devops.com.conf
```

#### 7. Test the Nginx Configuration for Syntax Errors
```bash
sudo nginx -t
```

#### 8. Restart Nginx to Apply Changes
```bash
sudo systemctl restart nginx
```

#### 9. Check Nginx Status to Ensure It's Running
```bash
sudo systemctl status nginx
```

#### 10. Adjust SELinux Settings (if applicable)
```bash
chcon -R -t httpd_sys_content_t /var/www/mero-afnai-devops.com/html
```

### Reference
- Official Nginx Documentation: [Nginx](https://www.nginx.com/resources/wiki/)
- Example Site: www.master-of-two-domain.com

### Additional Notes
- Ensure you have the correct permissions and ownership for the web directory.
- Verify the SELinux settings if SELinux is enabled on your system.
- Regularly check the status of Nginx to ensure your web server is running smoothly.