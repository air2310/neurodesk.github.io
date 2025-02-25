---
title: "Setup CVMFS Proxy"
linkTitle: "Proxy"
weight: 1
description: >
  Setup CVMFS Proxy server
---

If you want more speed in a region one way could be to setup another Stratum 1 server or a proxy. We currently don't run any proxy servers but it would be important for using it on a cluster.

```cmd
docker run --shm-size=1gb -it --privileged --user=root --name neurodesktop `
-v C:/neurodesktop-storage:/neurodesktop-storage -p 8888:8888 `
-e NEURODESKTOP_VERSION={{< params/neurodesktop/jupyter_neurodesk_version >}} `
vnmd/neurodesktop:{{< params/neurodesktop/jupyter_neurodesk_version >}}
```

# Setup a CVMFS proxy server
```bash
sudo yum install -y squid
```

Open the `squid.conf`and use the following configuration
```bash
sudo vi /etc/squid/squid.conf
```

```none
# List of local IP addresses (separate IPs and/or CIDR notation) allowed to access your local proxy
#acl local_nodes src YOUR_CLIENT_IPS

# Destination domains that are allowed
#acl stratum_ones dstdomain .YOURDOMAIN.ORG
#acl stratum_ones dstdom_regex YOUR_REGEX
acl stratum_ones dst 140.238.211.92

# Squid port
http_port 3128

# Deny access to anything which is not part of our stratum_ones ACL.
http_access deny !stratum_ones

# Only allow access from our local machines
#http_access allow local_nodes
http_access allow localhost

# Finally, deny all other access to this proxy
http_access deny all

minimum_expiry_time 0
maximum_object_size 1024 MB

cache_mem 128 MB
maximum_object_size_in_memory 128 KB
# 5 GB disk cache
cache_dir ufs /var/spool/squid 5000 16 256

```

```bash
sudo squid -k parse
sudo systemctl start squid
sudo systemctl enable squid
sudo systemctl status squid
sudo systemctl restart squid
```
