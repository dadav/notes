# firewalld

It's a firewall based on nftables developed by redhat. Changes are made with the firewall-cmd tool.

Rules are added to zones and zones are added to interfaces.

Pre-made rules are called services, so you can also add services to zones, e.g. the ssh service.

## Useful commands

```bash
# list the active zones
firewall-cmd --get-active-zones

# show the default zone (for new devices)
firewall-cmd --get-default-zone

# list the services [for $zone]
sudo firewall-cmd --list-services [--zone=$zone]

# show info about a service
sudo firewall-cmd --info-service $service

# add a service to a zone
sudo firewall-cmd --zone=$zone_name --add-service $service

# change the zone for an interface
sudo firewall-cmd --zone=$zone --change-interface=$interface_name
# via nm
nmcli connection modify myssid connection.zone home

# allow port 6443
sudo firewall-cmd --zone=$zone --add-port 6443/tcp

# port forwarding
sudo firewall-cmd --zone=public --add-forward-port=port=12345:proto=tcp:toport=22:toaddr=10.20.30.40

# make runtime config permanent
sudo firewall-cmd --runtime-to-permanent
```
