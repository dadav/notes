# docs
[VCL](https://varnish-cache.org/docs/trunk/users-guide/vcl.html)



# cache jpg's
```shell
sub vcl_recv {
  if(req.url ~ "\.(jpg|jpeg)(?=\?|&|$)"){
    unset req.http.cookie;
    return(hash);
  }
  return(pass);
}

sub vcl_backend_response {
  if(beresp.status < 400 && beresp.http.Cache-Control !~ "private" && beresp.http.Cache-Control != "no-cache") {
    if(bereq.url ~ "\.(jpg|jpeg)(?=\?|&|$)" && beresp.http.Content-Type ~ "image") {
      unset beresp.http.set-cookie;
      set beresp.ttl = 24h;
      return(deliver);
    }
  }
  set beresp.uncacheable = true;
  set beresp.ttl = 120s;
  return(deliver);
}
```

# considerate device type
```shell
sub vcl_recv {
  if(req.http.user-agent ~ "Phone|Android|Mobile"){
    set req.http.Device = "Mobile";
  } else {
    set req.http.Device = "Desktop";
  }
}

sub vcl_hash {
  hash_data(req.http.Device);
}
```

# don't cache if auth or cookie
```shell
sub vcl_recv {
    if (req.http.Authorization || req.http.Cookie) {
        /* Not cacheable by default */
        return (pass);
    }
}
```

# redirect
[source](https://varnish-cache.org/tips/vcl/redirect.html)
```shell
sub vcl_recv {
    if (req.url ~ "^/installation/ubuntu") {
        return (synth(301, "/releases/install_ubuntu.html"));
    }
    if (req.url ~ "^/installation/debian") {
        return (synth(302, "/releases/install_redhat.html"));
    }
}

sub vcl_synth {
    if (resp.status == 301 || resp.status == 302) {
        set resp.http.location = resp.reason;
        set resp.reason = "Moved";
        return (deliver);
    }
}
```
