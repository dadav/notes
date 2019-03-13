# show filebucket files
```shell
find /opt/puppetlabs/puppet/cache/clientbucket -type f -name paths -exec sort {} + | uniq -c | sort -nr
```
