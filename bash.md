# some bash functions i often use
```bash
# Waits for a port to be available
# $1 = Socket-IP
# $2 = Socket-Port
# $3 = Timeout; If expired, returns with 1
function wait_for_port() {
  ip=$1
  port=$2
  timeout=$3
  waited=0
  while ! nc -z $ip $port; do
    if [[ $waited -gt $timeout ]]; then
      return 1
    fi
    echo "Waiting for ${ip}:${port} to be available (${waited}/${timeout})"
    sleep 1
    waited=$((waited + 1))
  done
  return 0
}
```

# see my login-shell is doing
```BASH
PS4='+ $BASH_SOURCE:$LINENO:' BASH_XTRACEFD=7 bash -xlic "" 7>trace.log
less trace.log
```
