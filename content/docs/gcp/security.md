---
weight: 10300
title: "Security"
description: ""
icon: "article"
date: "2025-12-03T10:46:32+01:00"
lastmod: "2025-12-03T10:46:32+01:00"
draft: false
toc: true
---

## Compute

#### Brute force attack

##### Diagnosis

Brute force attack can spike your CPU usage. To stop brute force attacks, you must find out where those attacks come from. Run this command from your VM:

```bash
sudo cat /opt/bitnami/nginx/logs/access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head -10
```

Result:

```
69307 91.84.121.62
 5800 34.123.122.216
 1609 136.115.96.204
 1243 193.142.146.65
 1025 35.239.134.109
  605 79.154.68.51
  520 178.16.52.175
  412 66.249.70.3
  305 66.249.70.4
  286 114.119.142.179
```

The results show that IP 91.84.121.62 has hit 69307 times the VM. To obtain further details of what that IP address is targeting, run this command:

```bash
sudo grep "91.84.121.62" /opt/bitnami/nginx/logs/access.log | awk '{print $7}' | sort | uniq -c | sort -nr | head -5
```

Result:

```
5018 /register/elite-membership/
3641 /wp-admin/admin-ajax.php
3150 /register/pro-membership/
2057 /register/basic-membership/
1562 /contact-us/
```

##### Solution

Block that IP address completely. For that, create a firewall rule:

1. Go to **VPC Network**
2. Go to **Firewall**
3. Click on **Create firewall rule**
4. Edit these fields:
    - Name: `deny-91-84-121-62`
    - Direction of traffic: `Ingress`
    - Action to match: `Deny`
    - Targets: `All instances in the network`
    - Source IPv4 ranges: `91.84.121.62/32`
    - Protocols and ports: `Deny all`
5. Click on **Create** button
