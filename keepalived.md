# keepalived
## config
### basic
#### server1
```bash
vrrp_instance primary {
  state MASTER # Master/Slave
  interface eth1 # Floating IP interface
  virtual_router_id 50 # cluster id
  priority 10 # highest number becomes master
  advert_int 1 # advertisement interval
  
  authentication {
    auth_type PASS
    auth_pass mysecretpassword
  }
  
  virtual_ipaddress {
    192.168.50.10 # Floating IP
  }
}
```

#### server2
```bash
vrrp_instance primary {
  state MASTER
  interface eth1
  virtual_router_id 50
  priority 9
  advert_int 1 # advertisement interval
  
  authentication {
    auth_type PASS
    auth_pass mysecretpassword
  }
  
  virtual_ipaddress {
    192.168.50.10
  }
}
```

### process tracking
```bash
vrrp_script haproxy {
  script 'killall -0 haproxy'
  interval 2   # check every 2 seconds
  fall 2       # require 2 failures for KO
  rise 2       # require 2 successes for OK
}

vrrp_instance primary {
...
  track_script {
    haproxy
  }
}
```

### notification
```bash
vrrp_instance primary {
...
  notify /usr/local/bin/keepalivednotify.sh
}
```

/usr/local/bin/keepalivednotify.sh:
```bash
#!/bin/bash

TYPE=$1
NAME=$2
STATE=$3

case $STATE in
        "MASTER") /etc/init.d/apache2 start
                  exit 0
                  ;;
        "BACKUP") /etc/init.d/apache2 stop
                  exit 0
                  ;;
        "FAULT")  /etc/init.d/apache2 stop
                  exit 0
                  ;;
        *)        echo "unknown state"
                  exit 1
                  ;;
esac
```
