## Generate CA Certificate

```bash
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=NP/ST=KTM/L=KTM/O=F1Soft/OU=Personal/CN=mero.com" \
 -key ca.key \
 -out ca.crt
```

## Generate Server Key

```bash
openssl genrsa -out mero.com.key 4096
```

## Generate Certificate Signing Request (CSR)

```bash
openssl req -sha512 -new \
    -subj "/C=NP/ST=KTM/L=KTM/O=F1Soft/OU=Personal/CN=mero.com" \
    -key mero.com.key \
    -out mero.com.csr
```

## Generate Self-Signed Certificate

```bash
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=NP/ST=KTM/L=KTM/O=F1Soft/OU=Personal/CN=mero.com" \
 -key mero.com.key \
 -out mero.com.crt
```

## Create v3.ext File

```bash
cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=mero.com
DNS.2=mero
DNS.3=192.168.56.9
EOF
```

## Convert Certificate to PEM Format

```bash
openssl x509 -inform PEM -in mero.com.crt -out mero.com.cert
```

## Copy Certificate to Docker Directory

```bash
sudo cp mero.com.cert /etc/docker/certs.d/mero.com:443/
```

## Docker Login Credentials

- **Username:** `admin`
- **Password:** `Harbor12345`

> **Important:** Place the certificate in the following directory:
> 
> `/etc/docker/certs.d/yourdomain.com:443`

## Docker Login

```bash
docker login mero.com:443
Username: admin
Password: MeroReg111
```

**Note:** Your password will be stored unencrypted in `/home/anon/.docker/config.json`. Configure a credential helper to remove this warning. See [Docker Credential Store](https://docs.docker.com/engine/reference/commandline/login/#credentials-store).

## Tag and Push Docker Image

```bash
docker login
docker tag new_website mero.com/minato/zon:1.0         
docker push mero.com/minato/zon:1.0
```

for self signed
```
docker image ls
REPOSITORY                      TAG       IMAGE ID       CREATED          SIZE
mero/litte-fashion              latest    34ad9806c1a5   10 minutes ago   47.1MB
	[vagrant@master little_fashion]$ docker tag mero/litte-fashion selfhosted.harbor.com/library/little-fashion
[vagrant@master little_fashion]$ docker tag mero/litte-fashion selfhosted.harbor.com:443/library/little-fashion
[vagrant@master little_fashion]$ docker push selfhosted.harbor.com:443/library/little-fashion

```

## To access private repo from Kubernetes [Doc](https://docs.k3s.io/installation/private-registry)

```
kubectl create secret docker-registry regcred --docker-server=selfhosted.harbor.com:443 --docker-username=admin --docker-password=MeroReg111
```
## Image Pull Error 

Our k3s and k3s agent will look for /etc/rancher/k3s/registries.yaml
where
```
configs:
  selfhosted.harbor.com:443:
    tls:
      ca_file: /home/vagrant/certificates/ca.crt
```


```
ca.crt must be the same self created ca.crt according to harbor
scp ca.crt vagrant@192.168.56.12:/home/vagrant/certificates/
```

Now 
```
sudo systemctl restart k3s
sudo systemctl restart k3s-agent
```