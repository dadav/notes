# Runtime changes
Change properties on runtime
```shell
systemctl set-property --runtime name property=value
```

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
## Resources
```shell
# Per user/process limits
## Directive        ulimit equivalent     Unit
## LimitCPU=        ulimit -t             Seconds      
## LimitFSIZE=      ulimit -f             Bytes
## LimitDATA=       ulimit -d             Bytes
## LimitSTACK=      ulimit -s             Bytes
## LimitCORE=       ulimit -c             Bytes
## LimitRSS=        ulimit -m             Bytes
## LimitNOFILE=     ulimit -n             Number of File Descriptors 
## LimitAS=         ulimit -v             Bytes
## LimitNPROC=      ulimit -u             Number of Processes 
## LimitMEMLOCK=    ulimit -l             Bytes
## LimitLOCKS=      ulimit -x             Number of Locks 
## LimitSIGPENDING= ulimit -i             Number of Queued Signals 
## LimitMSGQUEUE=   ulimit -q             Bytes
## LimitNICE=       ulimit -e             Nice Level 
## LimitRTPRIO=     ulimit -r             Realtime Priority  
## LimitRTTIME=     No equivalent

# Cgroups
## CPU
CPUShares=1024
# or
CPUQuota=20%

## Memory
MemoryMax=1G

## Process count
TasksMax=1024

## IO
IOWeight=100
```

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

## User process
```bash
# /etc/systemd/logind.conf
KillUserProcesses=no

# enable user
loginctl enable-linger <username>

# create files
mkdir -p ~/.config/systemd/user/
touch ~/.config/systemd/user/blub.service
systemctl --user daemon-reload
systemctl --user start blub.service
```

## Script hacks
Sometimes you want to start multiple processes and exit if one of them fails.
```bash
#!/bin/bash

set -e

proc1 &
PID_PROC1=$!

proc2 &
PID_PROC2=$!

# watch the pids and exit if one exits
wait -n $PID_PROC1 $PID_PROC2
```
