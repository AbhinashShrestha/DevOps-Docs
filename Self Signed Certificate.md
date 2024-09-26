To generate a self-signed SSL certificate using OpenSSL and then make it trusted on your local machine (MacOS), follow these steps:
### Step 1: Generate the Self-Signed Certificate

1. **Install OpenSSL:**

   Ensure OpenSSL is installed on your Linux machine (Rocky Linux):

   ```bash
   sudo dnf install openssl
   ```

2. **Generate Private Key and Certificate Signing Request (CSR):**

   Generate a private key (`www.merai-webapp1.com.key`) and a CSR (`www.merai-webapp1.com.csr`):

   ```bash
openssl req -new -newkey rsa:2048 -nodes -keyout www.merai-rancher.com.key -out www.merai-rancher.com.csr
   ```

   You will be prompted to enter information about the certificate (Country, State, City, etc.). Fill in the details accordingly.

3. **Generate PEM File:**

   Convert the CSR into a PEM file (`www.merai-webapp1.com.pem`):

   ```bash
openssl req -in www.merai-rancher.com.csr -out www.merai-rancher.com.pem -outform PEM
   ```

4. **Generate Self-Signed Certificate (CRT):**
```
openssl x509 -req -sha256 -days 365 -in www.merai-rancher.com.csr -signkey www.merai-rancher.com.key -out www.merai-rancher.com.pem
```

   Generate a self-signed certificate (`www.merai-webapp1.com.crt`) using the private key:

   ```bash
   openssl x509 -req -in www.merai-webapp1.com.pem -signkey www.merai-webapp1.com.key -out www.merai-webapp1.com.crt
   ```

### Step 2: Copy Certificates to Your Local Machine

1. **SCP to Local Machine:**

   Copy the generated certificates to your local machine (MacOS). Replace `192.168.56.8` with your Linux server's IP address:

   ```bash
   scp root@192.168.56.8:/etc/ssl/www.merai-webapp1.com.* /Users/your_username/Desktop/Work/
   ```

   This command copies all files (`www.merai-webapp1.com.key`, `www.merai-webapp1.com.pem`, `www.merai-webapp1.com.crt`) to your local machine's `~/Desktop/Work/` directory.

### Step 3: Trust the Self-Signed Certificate on MacOS

1. **Import Certificate to Keychain:**

   Open the `www.merai-webapp1.com.crt` file on your MacOS. This should launch the Keychain Access app.

2. **Add to Keychain:**

   - Double-click the `www.merai-webapp1.com.crt` file.
   - In the popup window, choose "Add" for "System" or "Login" depending on where you want to install the certificate.
   - Authenticate with your MacOS credentials.

3. **Trust Certificate:**

   - After adding, double-click the certificate in Keychain Access.
   - Expand the "Trust" section.
   - For "Secure Sockets Layer (SSL)", select "Always Trust" from the dropdown.

4. **Restart Applications:**

   Restart any applications (like browsers) that need to use the certificate to apply the changes.

### Notes:

- **Security Warning:** Self-signed certificates are not trusted by default and are generally used for testing or internal purposes. For production, consider obtaining a certificate from a trusted Certificate Authority (CA).
- **Keychain Access Issues:** If you face issues with trusting the certificate via Keychain Access, ensure you have proper permissions and follow prompts correctly.

By following these steps, you can generate a self-signed SSL certificate using OpenSSL on Rocky Linux and make it trusted on MacOS for local testing and development purposes. Adjust paths and details as per your specific setup and requirements.