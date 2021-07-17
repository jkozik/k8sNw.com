# k8sNw.com
```
[jkozik@dell2 ~]$ docker tag jkozik/nw.com jkozik/nw.com:v1
[jkozik@dell2 ~]$ docker push jkozik/nw.com:v1
The push refers to a repository [docker.io/jkozik/nw.com]
fa2d6eb4bf15: Pushed
06724aa74b1f: Pushed
52f5931cc6db: Pushed
85278b4d9167: Pushed
a7479cdf12bb: Pushed
927232137b6b: Mounted from jkozik/chw.com
57797a33caa6: Mounted from jkozik/chw.com
8c1dd4e72f73: Mounted from jkozik/chw.com
40761247b349: Mounted from jkozik/chw.com
338ffd6b2db0: Mounted from jkozik/chw.com
65645e2d01df: Mounted from jkozik/chw.com
a7d7a796fe36: Mounted from jkozik/chw.com
c11e4ad5044b: Mounted from jkozik/chw.com
c907f065ed59: Mounted from jkozik/chw.com
1920409046ed: Mounted from jkozik/chw.net
1b04f74cccfe: Mounted from jkozik/chw.net
9f80175acb76: Mounted from jkozik/chw.net
4394b53db28c: Mounted from jkozik/chw.net
5b35dc81a1f5: Mounted from jkozik/chw.net
76d251b895a5: Mounted from jkozik/chw.net
7f5982d81e05: Mounted from jkozik/chw.net
ea7c1c005303: Mounted from jkozik/chw.net
df83fc87c7b6: Mounted from jkozik/chw.net
17e81ae0b70b: Mounted from jkozik/chw.net
e80100d0d319: Mounted from jkozik/chw.net
cc0f976c1376: Mounted from jkozik/chw.net
67415c00b86b: Mounted from jkozik/chw.net
ffc9b21953f4: Mounted from jkozik/chw.net
v1: digest: sha256:0bc0f01602602d30617b6a52385360fa9cb33d16cdc8cb4646d8a72aa4357a8d size: 6183
[jkozik@dell2 ~]$
```
[root@dell2 ~]# vi /etc/exports
/home/wjr/public_html    192.168.100.0/24(ro,sync,no_root_squash,no_all_squash)

[root@dell2 ~]# service nfs-server restart
Redirecting to /bin/systemctl restart nfs-server.service
You have new mail in /var/spool/mail/root
```

