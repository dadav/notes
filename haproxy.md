# haproxy
## config
TIP: You can check the config with `haproxy -f haproxy.cfg -c`

### global
The global section contains settings that apply to the HAProxy process itself.

It usually looks like this:
```bash
global
  log /dev/log local0 # Setup syslog
  log /dev/log local1 notice 
  chroot /var/lib/haproxy # Securityreasons
  stats socket /run/haproxy/admin.sock mode 660 level admin # Socket for interaction
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

It usually looks like this:
```bash
defaults
  log global # backreference to global section
  mode http # layer7
  option httplog # verbose
  option dontlognull # skip logging iif request doesnt send any data
  timeout connect 5000 # max millisecs to wait for a connection to a backend server
  timeout client 50000 # max millisecs to wait for a response from a client
  timeout server 50000 # max millisecs to wait for a response from a backendserver
  errorfile 400 /etc/haproxy/errors/400.http # errorfiles
  errorfile 403 /etc/haproxy/errors/403.http
  errorfile 408 /etc/haproxy/errors/408.http
  errorfile 500 /etc/haproxy/errors/500.http
  errorfile 502 /etc/haproxy/errors/502.http
  errorfile 503 /etc/haproxy/errors/503.http
  errorfile 504 /etc/haproxy/errors/504.http
  
```

### frontend
Defines a reverse proxy that will listen for incoming requests on a certain IP and port.

It usually looks like this:
```bash
frontend
  bind 127.0.0.1:81
  default_backend mywebservers
```

### backend
Defines a pool of servers that the frontend will forward requests to.

It usually looks like this:
```bash
backend mywebservers
  server nginx1 127.0.0.1:80
```

### listen
This section combines the frontend and backend into one.
