# Retry
Restart the service 3 times. Wait 90 seconds after each try.
```shell
Restart=always
RestartSec=90
StartLimitInterval=400
StartLimitBurst=3
```

# Graph
Show dependencies in a graph
```shell
systemd-analyze dot sshd.service | dot -Goverlap=false -Tpng | feh -
```
