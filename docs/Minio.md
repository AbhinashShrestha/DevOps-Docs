#### Setting Up Minio on macOS Host

Create necessary directories and run Minio using Docker:
```bash
mkdir -p ${HOME}/minio/data
docker run \
   -p 9000:9000 \
   -p 9001:9001 \
   --user $(id -u):$(id -g) \
   --name minio1 \
   -e "MINIO_ROOT_USER=ROOTUSER" \
   -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
   -v ${HOME}/minio/data:/data \
   quay.io/minio/minio server /data --console-address ":9001"
```

#### Access Credentials for Minio

- Username: `ROOTUSER`
- Password: `CHANGEME123`
- Access Key: `FGM8hqmybP7bQFoXIL1V`
- Secret Key: `kMvmUDG8DP1BSBzqQRKgL0vzL9nFOxOyXWxLct2z`

#### Docker Compose Alternative

Alternatively, use Docker Compose to start Minio:

```bash
docker-compose up -d --build
```

#### Accessing Minio Server

Access Minio server at:
```plaintext
http://127.0.0.1:9000
```

#### Setting Up Minio Client (mc) on Rocky Linux Server

Download and configure Minio Client (mc) on the server:

```bash
curl https://dl.min.io/client/mc/release/linux-amd64/mc --create-dirs -o $HOME/minio-binaries/mc
chmod +x $HOME/minio-binaries/mc
export PATH=$PATH:$HOME/minio-binaries/
mc --help
```

#### Configuring Minio Client (mc) Alias

Set up Minio client alias to connect to the Minio server:

```bash
mc alias set myminio http://192.168.56.1:9000 FGM8hqmybP7bQFoXIL1V kMvmUDG8DP1BSBzqQRKgL0vzL9nFOxOyXWxLct2z
```

#### Using Minio Client (mc) Commands

Example commands for Minio client (mc):

- **Get**: Retrieve a file from Minio server
  ```bash
  mc cp myminio/thikxa/CentOS-7-x86_64-Minimal-2009.iso /home/anon
  ```

- **Put**: Upload a file to Minio server
  ```bash
  mc cp /home/anon/a.txt myminio/thikxa
  ```

### Notes

- Replace placeholders (`ROOTUSER`, `CHANGEME123`, `192.168.56.1:9000`, etc.) with your actual credentials and server details.
- Ensure Docker and Docker Compose are properly installed and configured on your host and server environments.
- Customize paths and configurations as per your specific setup and requirements.