# haproxy
## config
### global
The global section contains settings that apply to the HAProxy process itself.

It usually looks like this:
```bash
global
  log /dev/log local0
  log /dev/log local1 notice
  chroot /var/lib/haproxy
  stats socket /run/haproxy/admin.sock mode 660 level admin
  stats timeout 30s
  user haproxy
  group haproxy
  daemon
  
  # TLS (https://hynek.me/articles/hardening-your-web-servers-ssl-ciphers/)
  ca-base /etc/ssl/certs
  crt-base /etc/ssl/private
  
  ssl-default-bind-options no-sslv3
  ssl-default-bind-ciphers ECDH+AESGCM:ECDH+CHACHA20:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:RSA+AESGCM:RSA+AES:!aNULL:!MD5:!DSS

  ssl-default-server-options no-sslv3
  ssl-default-server-ciphers ECDH+AESGCM:ECDH+CHACHA20:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:RSA+AESGCM:RSA+AES:!aNULL:!MD5:!DSS
   
  tune.ssl.default-dh-param 2048
```

### defaults
Here we can configure settings that a reused across all of the proxies.

### frontend
Defines a reverse proxy that will listen for incoming requests on a certain IP and port.

### backend
Defines a pool of servers that the frontend will forward requests to.

### listen
This section combines the frontend and backend into one.
