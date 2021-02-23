# wiki
https://wiki.archlinux.org/index.php/squid

# options you probably want to use
```squidconf
# in-memory cache
cache_mem 64 MB
# upstream proxy
cache_peer 10.1.1.100 parent 8080 0 no-query default login=user:password
# tells the cache to never go direct to the internet to retrieve a page
never_direct allow all
# max object size in cache
maximum_object_size 10 MB
# cache directory/object (in MB)
cache_dir diskd /cache0 200000 16 256
```

# commands you might want to use
```bash
# syntax check
squid -k check

# create cache directories
squid -z

# reload config
squid -k reconfigure

# utilization
squidclient -h localhost cache_object://localhost/ mgr:utilization

# some infos
squidclient -h localhost cache_object://localhost/ mgr:info
```
