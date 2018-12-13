# haproxy
## config
TIP: You can check the config with `haproxy -f haproxy.cfg -c`

#### sections
##### peers
The peers section lists multiple haproxy machines and makes it possible to share stick-tables. The peer labes should match the machinenames.

```bash
peers mypeers
        peer hostname1 192.168.50.10:50000
        peer hostname2 192.168.50.11:50000

listen mywebserver
        bind *:80 transparent
        balance roundrobin
        server webserver 192.168.50.12:8080 check
        stick-table type ip size 1m size 1m expire 30m peers mypeers
        stick on src
        
```

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

###### Tips
Since version x.x haproxy supports multiprocess & multithreading.

*BUT*

*Caveat 1:* multi-process mode does not support synchronizing stick tables over multiple processes so each process will have it's own making it useless for persistence. However, HTTP cookies are supported so some persistence options do exist.

*Caveat 2:* multi-process mode means the stats page only displays one process per port, and requires additional config to show the different process stats
You can set these options like this:

```bash
global
  nbproc 1 # 1 Process
  nbthread 4 # 4 Threads
  cpu-map auto:1/1-4 0-3 # Map threads to first 4 CPU cores (0-3)
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

You can inspect the url with the following fetch methods:

| Method        | Info         |
| ------------- |-------------|
| path      | exact string match |
| path_beg      | URL begins with string |
| path_dir      | subdir match |
| path_end      | suffix match |
| path_len     | length match |
| path_reg      | regex match |
| path_sub      | substring match |

Some examples:
```bash
acl isimage path_reg (png|jpg|jpeg|gif)$
acl islongerthan4 path_len gt 4
acl isoldapi path_beg -i /api/v1
acl isnewapi path_beg -i /api/v2
```

You could also use a URL parameter:
```bash
acl iswest url_param(region) -m str west # http://example.com/?region=west
acl iswest url_param(region) -i -m str west # ignore case: http://example.com/?region=West
acl iswest url_param(region) -i -m str west westcoast wc # multiple choices: http://example.com/?region=WC
acl iswest url_param(region) -m reg .+ # any value: http://example.com/?region=asdhrt24r
```

By headers:

```bash
acl ismobile req.hdr(User-Agent) -i -m reg (android|iphone)
acl ishost1 req.hdr(Host) -i -m str www.example.com
```

#### options
##### health
You can check the backend servers like this (TCP):
```bash
# downinter 1m: check down server every 1m
# inter 10s: check every 10 seconds
# fall 2: server is seen as down if test failed 2 times
# rise 5: server is seen as up if test succeded 5 times
server web1 192.168.50.10:80 check downinter 1m inter 10s fall 2 rise 5
```

In http mode you have better check options:
```bash
backend webservers
  mode http
  balance roundrobin
  server web1 192.168.50.10:80 check
  option httpchk HEAD /testpage HTTP/1.1\r\nHost:\ mywebsite.com
  # Optional tell haproxy which status is ok
  http-check expect status 200 # only 200
  # http-check expect rstatus ^2(\d){2}$ # 2xx
```

You can also rely on another server (from a backend):
```bash
backend reserv_service
  mode http
  server rs1 192.168.50.10:80 track databases/db1
  
 backend databases
  mode tcp
  server db1 192.168.60.10:1433 check # must use check here
```

You can also rely on another server (from a frontend):
```bash
frontend website
  bind *:80
  acl dbon srv_is_up(primary_databases/db1)
  use_backend primary_service if dbon
  default_backend backup_server
```

##### persistency
With the following options you can tell haproxy how it should remember a server.

###### cookie
You can insert a cookie.

```bash
backend webservers
  # indirect: remove cookie before send request to backends
  # nocache: send private headers
  cookie SERVERUSED insert indirect nocache
  server web1 192.168.50.10:80 check cookie web1
```

###### stick-table
A stick-table is a databse within haproxy.

```bash
backend webservers
  mode tcp
  server web1 192.168.50.10:80 check
  server web2 192.168.50.11:80 check
  stick-table type ip size 1m expire 30m
  stick on src
```

You can also put it in an extra backend

```bash
backend mystorage
  stick-table type ip size 1m expire 30m
  
backend webservers
  mode tcp
  server web1 192.168.50.10:80 check
  server web2 192.168.50.11:80 check
  stick on src table mystorage
  # Dont wait for check to fail. If we cant connect, choose another server.
  option redispatch
```

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

### Logging
#### rsyslog
```bash
# If you chroot haproxy, you need this:
$AddUnixListenSocket /var/lib/haproxy/dev/log

if $programname startswith 'haproxy' then /var/log/haproxy.log
& stop
```

TIP: You can use `log-tag` to differentiate each section in haproxy.

If you want to split the error-logs of haproxy into different files, use the `log-separate-errors` option in the haproxy-config.
In the rsyslog- config do the following:

```bash
if $programname startswith 'haproxy' then /var/log/haproxy.log

if $programname startswith 'haproxy' and $syslogseverity <= 3 then /var/log/haproxy_errors.log
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

#### Redirect
```bash
frontend mywebsite
  bind *:80
  default_backend webservers
  acl isoldsite req.hdr(Host) -i -m str oldsite.com
  redirect prefix http://newsite.com if isoldsite # http://oldsite.com/?test=1 will be http://newsite.com/?test=1
```

You could drop the query with `redirect prefix ... drop-query` or by using `redirect location`

#### Rate limit
```bash
frontend mywebsite
  bind *:80
  default_backend webservers
  stick-table type ip size 200k expire 120s store conn_rate(3s),bytes_out_rate(60s),http_req_rate(3s)
  http-request track-sc0 src
  http-request deny if { sc0_conn_rate gt 60 } # Deny if opened more than 60 connections within 3s
  http-request deny if { sc0_bytes_out_rate gt 10000000 } # Deny if transfered more than 10M within 60s
  http-request deny if { sc0_http_req_rate ge 60 } # Deny if more than 60 requests in 3 secs
```

#### Remember the attack
```bash
frontend mywebsite
  bind *:80
  default_backend webservers
  stick-table type ip size 200k expire 2m store conn_rate(3s),gpc0 # save the connectionrate and a boolean
  acl conn_rate_abuse sc0_conn_rate gt 60
  acl flag_as_abuser sc0_inc_gpc0 gt 0 # increase counter
  acl is_abuser src_get_gpc0 gt 0 # check counter
  http-request track-sc0 src
  http-request deny if is_abuser
  http-request deny if conn_rate_abuse flag_as_abuser # mark user
```
