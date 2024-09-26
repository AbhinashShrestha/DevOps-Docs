- Set up 2 website in nginx in separate ports
- Install HAProxy 
	- Add the following in /etc/haproxy/haproxy.cfg
```
/etc/haproxy/haproxy.cfg
	frontend main
    bind *:80
    timeout client 60s
    mode http
    default_backend rockylinux9_apps
    backend rockylinux9_apps
    timeout connect 30s
    timeout server 100s
    mode http
    balance roundrobin
    server node1 127.0.0.1:1111 check
    server node2 127.0.0.1:2222 check
```
