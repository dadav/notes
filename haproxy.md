# haproxy
## config
TIP: You can check the config with `haproxy -f haproxy.cfg -c`

#### sections
##### global
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

##### defaults
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

##### frontend
Defines a reverse proxy that will listen for incoming requests on a certain IP and port.

It usually looks like this:
```bash
frontend
  bind 127.0.0.1:81
  default_backend mywebservers
```

#### backend
Defines a pool of servers that the frontend will forward requests to.

It usually looks like this:
```bash
backend mywebservers
  server nginx1 127.0.0.1:80
```

##### listen
This section combines the frontend and backend into one.

It usually looks like this:
```bash
listen myproxy
  bind 127.0.0.1:81
  server nginx1 127.0.0.1:80
```
#### syntax
##### acl
There are two types of acls:
named: `acl is_localhost src 127.0.0.1`
and anonymous: `use_backend localhost_server if { src 127.0.0.1 }`

You can combine multiple acls like this 
```bash
acl is_localhost src 127.0.0.1
acl is_get method GET
use_backend test if is_localhost is_get

# You can even use 'or'
use_backend test2 if is_localhost or is_get
```

#### options
##### algorithms
###### roundrobin
Split the requests equally on every server. You can put a weight option on each server.

```bash
backend webservers
  balance roundrobin
  server web1 192.168.50.10:80 weight 1 # gets 25%
  server web2 192.168.50.11:80 weight 3 # gets 75%
```

###### leastconn
Use the server with the least connections.

```bash
backend webservers
  mode tcp
  balance leastconn
  server database1 192.168.50.10:1433 check slowstart 60s
```

The `slowstart` options tells haproxy to progressivly give this server more connections. You could also set `weights` here, if you want to take different hardware into account.

###### uri
This algorithm looks at the path of the URL being requested, which includes everything after the first forward slash and before the first questionmark.

```bash
backend cache_servers
  balance uri # optionally you can set the 'whole' option to use the whole URL as hash
  server cache1 192.168.168.50.20:80 weight 1
  server cache1 192.168.168.50.21:80 weight 2
``` 
###### first
Use the first server in the line. If its full, use the next one.
It's important to set the maxconn option on every server. Otherwise it would serve all requests to the first server using the global maxconn option (web2 never would be used).

```bash
backend webservers
  balance first
  server web1 192.168.50.10:80 maxconn 30
  server web2 192.168.50.11:80 maxconn 30
  server web3 192.168.50.12:80 maxconn 30
```

### Tips
#### Forward IP
```bash
# httpmode
frontend
  mode http
  option forwardfor
  http-request set-header Forwarded for=%[src]

# proxymode
backend test
  mode tcp
  server web 192.168.40.10:80 send-proxy
```
