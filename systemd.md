
# Graph
Show dependencies in a graph
```shell
systemd-analyze dot sshd.service | dot -Goverlap=false -Tpng | feh -
```

# Debug
Start unit in debug mode
```shell
SYSTEMD_LOG_LEVEL=debug /lib/systemd/systemd-networkd
```
# List
List enabled units
```shell
systemctl list-unit-files --state=enabled
```

# Unit tweaks
## Retry
Restart the service 3 times. Wait 90 seconds after each try.
```shell
Restart=always
RestartSec=90
StartLimitInterval=400
StartLimitBurst=3
```

## Escaping
You can escape strings with
```shell
systemd-escape <string>
```
Use that escaped string in your service name
```shell
servicename@escapedstring.service
```
You can use that escaped string in your service like this:
```shell
%i -> still escaped
%I -> unescaped
```
