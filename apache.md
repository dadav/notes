# security
see [Apache hardening](https://geekflare.com/apache-web-server-hardening-security/)

```conf
# disable banner
ServerTokens Prod
ServerSignature Off

# disable listing
<Directory /opt/apache/htdocs>
Options -Indexes
</Directory>

# disable etag vulns
FileETag None

# no htaccess
<Directory /> 
Options -Indexes 
AllowOverride None
</Directory>

# limit to GET POST HEAD
<LimitExcept GET POST HEAD>
deny from all
</LimitExcept>

# no tracing
TraceEnable off

# set secure cookie (needs a2enmod headers)
Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure

# Clickjacking (needs a2enmod headers)
Header always append X-Frame-Options SAMEORIGIN

# xss (needs a2enmod headers)
Header set X-XSS-Protection "1; mode=block"

# no include
<Directory /opt/apache/htdocs>
Options –Indexes -Includes
Order allow,denyAllow from all
</Directory>

# no http 1.0 (needs a2enmod rewrite)
RewriteEngine On
RewriteCond %{THE_REQUEST} !HTTP/1.1$
RewriteRule .* - [F]

# set low timeout (slow loris)
Timeout 60

# ssl
SSLEngine On
SSLCertificateFile #Certificate signed by authority
SSLCertificateChainFile #Certificate signer given by authority
SSLCertificateKeyFile #Key file which you generated above
SSLCipherSuite HIGH:!MEDIUM:!aNULL:!MD5:!RC4
SSLProtocol –ALL +TLSv1.2
```

# mod_security2
```shell
a2enmod unique_id
a2enmod security2
```

`/etc/apache2/conf.d/mod_security2.conf`
```conf
# activate rules
SecRuleEngine On

# disable Server header
SecServerSignature " "
```

