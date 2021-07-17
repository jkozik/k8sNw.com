# k8sNw.com
Setup napervilleweather.com in a kubernetes cluster as deployment named nwcom; expose as service nwcom; connect to the internet as napervilleweather.com through an ingress called nwcom-ingress. Get realtime weather data from an NFS mount on my local host through the PV/PVC named nwcom-persistent-storage.

Source image [InstallNW.com](https://github.com/jkozik/InstallNW.com)

# build image, put on docker hub
NapervilleWeather.com is running in a docker container on my host, directly -- not in a VM.  To make it run in my kubernetes cluster, I need to push the image to my dockerhub repository.  The kubernetes deployment resource has an "image" field that triggers a pull from dockerhub.
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

```
# Setup NFS share on host
The weather computer (outside of the cluster), runs a program called Cumulus.  It is designed to upload realtime weather data and graphs frequently to a website.  In my case, I have a login on a host dedicated to that website, user=wjr. The path to the website is /home/wjr/public_html. I want to make that data visible to the deployment that I want to run inside my cluster.  First step is to make an NFS share out of the data.  From the root login of my host (not in the cluster):
```
[root@dell2 ~]# vi /etc/exports
/home/wjr/public_html    192.168.100.0/24(ro,sync,no_root_squash,no_all_squash)

[root@dell2 ~]# service nfs-server restart
Redirecting to /bin/systemctl restart nfs-server.service

```
## Deploy
Clone the yaml files in this repository.  One can apply them one at a time, but it works fine to apply them all at once.  On the kubectl host, run the following to deploy all in one command:
```
[jkozik@dell2 ~]$ git clone https://github.com/jkozik/k8sNw.com
Cloning into 'k8sNw.com'...
remote: Enumerating objects: 13, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 13 (delta 1), reused 7 (delta 1), pack-reused 0
Unpacking objects: 100% (13/13), done.

[jkozik@dell2 ~]$ cd k8sNw.com
[jkozik@dell2 k8sNw.com]$ ls
nwcom-deploy.yml  nwcom-ingress.yml  nwcom-pvc.yml  nwcom-pv.yml  nwcom-svc.yml  README.md

[jkozik@dell2 k8sNw.com]$ kubectl apply -f .
deployment.apps/nwcom created
ingress.networking.k8s.io/nwcom-ingress created
persistentvolume/nwcom-persistent-storage created
persistentvolumeclaim/nwcom-persistent-storage created
service/nwcom created
```
## Verify that the application is running
```

[jkozik@dell2 k8sNw.com]$ kubectl get service,deployment,ingress,pv,pvc
NAME                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/nwcom             NodePort    10.100.137.46    <none>        80:31241/TCP   84s

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nwcom                 1/1     1            1           86s

NAME                                                CLASS    HOSTS                     ADDRESS           PORTS   AGE
ingress.networking.k8s.io/nginx-wordpress-ingress   <none>   k8s.kozik.net             192.168.100.174   80      21d
ingress.networking.k8s.io/nwcom-ingress             <none>   napervilleweather.com     192.168.100.174   80      86s

NAME                                            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                  STORAGECLASS   REASON   AGE
persistentvolume/nwcom-persistent-storage       1Gi        ROX            Retain           Bound    default/nwcom-persistent-storage       nfs                     84s

NAME                                                 STATUS   VOLUME                         CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/nwcom-persistent-storage       Bound    nwcom-persistent-storage       1Gi        ROX            nfs            84s
```
## Check the web page http://napervilleweather.com
```
[jkozik@dell2 k8sNw.com]$ curl napervilleweather.com | head
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
   "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <head>
    <!-- ##### start AJAX mods ##### -->
    <script type="text/javascript" src="ajaxCUwx.js"></script>
    <!-- AJAX updates by Ken True - http://saratoga-weather.org/wxtemplates/ -->
    <script type="text/javascript" src="ajaxgizmo.js"></script>
    <script type="text/javascript" src="language-en.js"></script>
        <!-- language for AJAX script included -->
100  8004    0  8004    0     0  30102      0 --:--:-- --:--:-- --:--:-- 30090
    
```
## HomeLAN NATing from external IP address / port 80 to cluster's LAN address and ingress controller's port number.
So, on my home LAN http://192.168.100.174:30410 is where incoming web traffic enters.  The ingress controller parses for napervilleweather.com and redirects the traffic to the nwcom service. I have an external IP address for my home LAN that gets NAT'd by my home router to 192.168.100.174.  On that NAT box, I map port 80 to port 30410. As you can see from the get ingress command, I also am running a wordpress application with another URL, but also mapped to the same IP address and port number.  The ingress control does a reverse proxy function and splits the wordpress traffic off to the nginx-wordpress-ingress resource.
