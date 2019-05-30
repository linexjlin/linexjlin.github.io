---
layout: post
title: Using poste.io to build a mail server
---

# Set PTR
PTR or reverse DNS or rDNS tell the domain name of  your IP, you may set this record in your VPS console like this ![](https://ipfs.ee/ipfs/QmR5Fi87zhY4CSiZM7snJzuumZveNFxAo5mwZP6fVAkj1U?name=2019-01-08_12-00-16.png) or if it is not supported by your ISP just submmit a ticket to apply it.

# DNS Records

type | record | value | ttl
-|-|-|-
A|@|130.255.76.237| 600
A|mail|130.255.76.237|600
A|  www | 130.255.76.237| 600
cname| smtp | 130.255.76.237| 600
cname| pop | 130.255.76.237| 600
cname | imap | 130.255.76.237| 600
mx | @| mail.dba.pw | 600
text | @|  "v=spf1 mx ~all" | 600
text | _dmarc.dba.pw | "v=DMARC1; p=none; rua=mailto:dmarc-reports@dba.pw"| 600
text |s20190305648._domainkey.dba.pw | "xxxx"  | 600

# start poste.io docker

```
docker run \
-p 25:25 \
-p 80:80 \
-p 110:110 \
-p 143:143 \
-p 443:443 \
-p 587:587 \
-p 993:993 \
-p 995:995 \
-v /etc/localtime:/etc/localtime:ro \
-v /var/maildata:/data \
--name "mail" \
-h "mail.dba.pw" \
-t analogic/poste.io
```

# Get certificates  from "Let's encrypt"
Loin to admin panel via like https://mail.dba.pw
go to like  https://mail.dba.pw/admin/le/
![](https://ipfs.ee/ipfs/QmYtkatZP198mzdWHQanwL1WV7ripgWGvD1zDCD9dmuyc8?name=2019-01-08_12-10-06.png)

# Webmail 
https://mail.dba.pw/webmail
![](https://ipfs.ee/ipfs/QmYzt99T3YrFVWZL1LusobNSbTcdjshAq3h8rEQ4qRgBBG?name=2019-01-08_13-07-09.png)
![](https://ipfs.ee/ipfs/QmQAHK1jfTp8hPvfzQw7qmSHt7jvNeXvmxQVQVtUEDCZaC?name=2019-01-08_13-07-58.png)
![](https://ipfs.ee/ipfs/QmciyQEY1eSSD7WrQGFVSX9nS1XUAKN6q2BDQyxi2JxBd4?name=2019-01-08_13-08-16.png)

# Admin panel
https://mail.dba.pw/admin/login
![](https://ipfs.ee/ipfs/QmejZDNKrKfmukQW4ydRhgqBsz2HHrtVbDktYf1HTnJ1qg?name=2019-01-08_13-09-34.png)
![](https://ipfs.ee/ipfs/QmWnCaPXg9nFGjpsNjyT78BR8fa9c2BNLaybgnSkRNy2MZ?name=2019-01-08_13-09-11.png)

# client 
## pop
Note: Use POP protocol will keep all mails in local machine and delete from server side.
server: pop.dba.pw:110
username: line@dba.pw
connection security: starttls
![](https://ipfs.ee/ipfs/QmdciuTv8kPQ1F7s6LMKz7DYadGpyggsazZiXeCQeJk43D?name=2019-01-08_11-33-09.png)
## imap
server: imap.dba.pw:143
username: line@dba.pw
![](https://ipfs.ee/ipfs/QmRBMzdq4L2EEA3HfzJ9rboiy5HsG18qgKrrGS6dbCPW41?name=2019-01-08_11-44-08.png)
## smtp
serve: smtp.dba.pw:587
username: line@dba.pw
connection security: startttls
![](https://ipfs.ee/ipfs/QmUVSUtQA9n6698juJw1GASbNsm94z7GJbR4Mugai54f8H?name=2019-01-08_11-30-19.png)

# Refer 
refer1: https://qing.su/article/139.html
refer2: https://poste.io/doc/client-settings
