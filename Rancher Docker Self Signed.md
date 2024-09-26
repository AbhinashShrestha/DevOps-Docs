## [Doc](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/other-installation-methods/rancher-on-a-single-node-with-docker#:~:text=After%20creating%20your%20certificate%2C%20run,mount%20them%20in%20your%20container.&text=The%20path%20to%20the%20directory%20containing%20your%20certificate%20files.&text=The%20path%20to%20your%20full%20certificate%20chain.)

```
docker run -d --restart=unless-stopped   -p 8080:80 -p 3443:443   -v /home/vagrant/rancher.watashi.crt:/etc/rancher/ssl/cert.pem   -v /home/vagrant/rancher.watashi.pem:/etc/rancher/ssl/key.pem   --privileged   rancher/rancher:v2.4.9  --no-cacerts
```


username: admin
password: admin

