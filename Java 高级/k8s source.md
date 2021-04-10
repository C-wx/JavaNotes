![image-20210407204617341](https://gitee.com/cbuc/picture/raw/master/typora/20210407204625.png)





flannel 安装  ：[下载地址](https://github.com/flannel-io/flannel/releases/)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407215040.png)

```shell
docker load < flanneld-v0.13.0-amd64.docker
```

![image-20210407215330350](https://gitee.com/cbuc/picture/raw/master/typora/20210407215330.png)



执行命令

```shell
kubectl apply -f kube-flannel.yml
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407215823.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407215910.png)





```shell
[root@master ~]# kubectl create deployment nginx --image=nginx:1.14-alpine
deployment.apps/nginx created
```

```shell
[root@master ~]# kubectl get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           31s
```

```shell
[root@master ~]# kubectl expose deploy nginx --port=80 --target-port=80 --type=NodePort
service/nginx exposed
```

```shell
[root@master ~]# kubectl get svc 
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
nginx        NodePort    10.110.224.214   <none>        80:31771/TCP   5s
```

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407220615.png)

![](https://gitee.com/cbuc/picture/raw/master/typora/20210407220656.png)