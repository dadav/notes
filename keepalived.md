# keepalived
## config
### server1
```bash
vrrp_instance primary {
  state MASTER # Master/Slave
  interface eth1 # Floating IP interface
  virtual_router_id 50 # cluster id
  priority 10 # highest number becomes master
  authentication {
    auth_type PASS
    auth_pass mysecretpassword
  }
  virtual_ipaddress {
    192.168.50.10 # Floating IP
  }
}
```

### server2
```bash
vrrp_instance primary {
  state MASTER
  interface eth1
  virtual_router_id 50
  priority 9
  authentication {
    auth_type PASS
    auth_pass mysecretpassword
  }
  virtual_ipaddress {
    192.168.50.10
  }
}
```
