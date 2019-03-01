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
0/0/0/70/70                       # timers (TR / Tw / Tc / Tr / Ta)
# TR: Client request time
# Tw: Queues time
# Tc: Connection time (to final server)
# Tr: Server response time
# Ta: Request active time (first bytes send - last byte received)
200                               # status_code
322                               # bytes_read
-                                 # captured_request_cookie
-                                 # captured_response_cookie
----                              # termination_state
10/10/0/1/0                       # counters (actconn / feconn / beconn / srv_conn / retries)
# actconn: connections on the process
# feconn: connections on frontend
# beconn: connections on backend
# srv_conn: active connections
# retries: ~~~
0/0                               # srv_queue / backend_queue
{}                                # captured_request_headers
{}                                # captured_response_headers
"GET /vmweb/vmwebHTTP/1.1"        # http_request
```
