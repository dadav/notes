# some useful oneliners

## remove invalid image files
```bash
fd -e jpg -e jpeg -e png -0 | xargs -0 -r -P8 -I{} sh -c "identify -regard-warnings -- {} &>/dev/null || { echo {} is invalid. I will remove it now; rm -- {}"
```

## show all processes running in a container

```bash
ps -e -o pid,cgroup | grep "/docker/" | awk '{ print $1 }' | paste -sd, | xargs -r -n1 -I{} ps -p{} u
```
