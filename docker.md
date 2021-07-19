# show all processes running in a container
```bash
ps -e -o pid,cgroup | grep "/docker/" | awk '{ print $1 }' | paste -sd, | xargs -r -n1 -I{} ps -p{} u
```
