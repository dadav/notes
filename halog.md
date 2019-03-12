# halog
## httplog
```bash
2019-03-01T01:43:36.110655+01:00  # timestamp
valueaa1pu                        # hostname
haproxy[8517]:                    # process_name [pid]
10.10.120.208:63954               # client_ip : client_port
[01/Mar/2019:01:43:36.041]        # request_date
value_front_https~                # frontend
value_back_https/valueas3         # backend / server_name
 0/0/0/70/70                      # timers (TR / Tw / Tc / Tr / Ta)
#| | |  |  |--Ta: Request active time (first bytes send - last byte received)    
#| | |  |-----Tr: Server response time      
#| | |--------Tc: Connection time (to final server)
#| |----------Tw: Queues time
#|------------TR: Client request time
200                               # status_code
322                               # bytes_read
-                                 # captured_request_cookie
-                                 # captured_response_cookie
----                              # termination_state (https://cbonte.github.io/haproxy-dconv/1.8/configuration.html#8.5)
10/10/0/1/0                       # counters (actconn / feconn / beconn / srv_conn / retries)
#|  | | | |--retries: ~~~
#|  | | |----srv_conn: active connections
#|  | |------beconn: connections on backend
#|  |--------feconn: connections on frontend 
#|-----------actconn: connections on the process
0/0                               # srv_queue / backend_queue
{}                                # captured_request_headers
{}                                # captured_response_headers
"GET /vmweb/vmwebHTTP/1.1"        # http_request
```

### Examples
```bash
# show top 5 urls with error, sort by request count
<haproxy.log halog -s -1 -u -e -q | sort -n | tail -5

# show missing files
<haproxy.log halog -s -1 -u -q -H -hs 404

# server ran in timeout
<haproxy.log halog -s -1 -q -tcn sH

# Queued connections count
<haproxy.log halog -s -1 -q -Q -c
```

