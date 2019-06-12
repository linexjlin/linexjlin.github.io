---
layout: post
title: Using ipfs to create a file or image bed
---

## run ipfs IPFS conf
``` 
ipfs init 
ipfs daemon
```
## caddy.conf
```
ipfs.ee {
        header / Access-Control-Allow-Origin *

        rewrite {
                r /ipfs/(.*)/(.*)
                to /ipfs/{1}
        }

        proxy /ipfs 127.0.0.1:8080 {
                transparent
        }

        proxy /api/v0/add 127.0.0.1:5001 {
                transparent
        }

        log /var/log/caddy/ipfs.ee.log
}
```
## Use ShareX to automative upload screenshot or file 
![](https://ipfs.ee/ipfs/QmVZiSJAawVARErjxACEbUVUqUmgd3rXka9nzttPYhPgXM/2019-05-30_21-24-12.png)
![](https://ipfs.ee/ipfs/QmRxaucuukw9uygTujqRKvGMDNKaznY5J8ck1hDQjbQAW8/2019-05-30_21-24-25.png)
