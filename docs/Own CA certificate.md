```
#!/bin/bash

set -x
openssl genrsa -out ca.key 4096

echo -e "\n------------\n Creating Root CA Certificate \n-----------------\n "
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=NP/ST=Bagmati Pradesh/L=Kathmandu/O=OreNoKaisha/OU=Personal/CN=watashiCA.com" \
 -key ca.key \
 -out ca.crt

echo -e "\n------------\n Generating Server Certificate \n-----------------\n"
openssl genrsa -out rancher.watashi.key 4096
openssl req -sha512 -new \
    -subj "/C=NP/ST=Bagmati/L=Kathmandu/O=Watashi/OU=OreNoKaisha/CN=rancher.watashi" \
    -key rancher.watashi.key \
    -out rancher.watashi.csr

cat > rancher.watashi.v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=rancher.watashi
EOF

echo -e "\n------------\n Signing Server Certificate with CA's Certificate \n-----------------\n"
openssl x509 -req -sha512 -days 3650 \
    -extfile rancher.watashi.v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in rancher.watashi.csr \
    -out rancher.watashi.crt

echo -e "\n------------\n Generating Client PEM file \n-----------------\n"
cat rancher.watashi.key rancher.watashi.crt > rancher.watashi.pem

chmod 400 rancher.watashi.key

echo -e "\n------------\n Certificate Generation Completed \n-----------------\n"

```
