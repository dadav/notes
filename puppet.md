# show filebucket files
```shell
puppet filebucket list --local
# oder
find /opt/puppetlabs/puppet/cache/clientbucket -type f -name paths -exec sort {} + | uniq -c | sort -nr
```
