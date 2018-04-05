# hadoop-openshift
setting up hadoop in my test lab


## Create a Project
create the project the serviceaccount
```shell
oc new-project hadoop
oc create sa hadoop
oc login -u system:admin
oc adm policy add-scc-to-user anyuid -z hadoop
```
use oc edit scc anyuid to enable hostpath plugin,  this is for allowing hadoop container to access hostpath /hadoop/dfs
```shell
oc edit scc anyuid
```
the anyuid scc will be looks like following
only  set allowHostDirVolumePlugin: true and leave other things no change

```shell
allowHostDirVolumePlugin: true
allowHostIPC: false
......
fsGroup:
  type: RunAsAny
groups:
- system:cluster-admins
kind: SecurityContextConstraints
......
- hostPath
- persistentVolumeClaim
- projected
- secret
```

lable the node for pod to be schedulable (required from ocp 3.9)
```shell
oc lable node node1.ocp37.com node-role.kubernetes.io/compute=true 
oc lable node node1.ocp37.com node-role.kubernetes.io/compute=true 
```

we are going to use below image from docker,  replace the image for your own installation in datanode.yml and namenode.yml  
```shell
docker.io/uhopper/hadoop-namenode:2.7.2
docker.io/uhopper/hadoop-namenode:2.7.2
```
now we can go ahead and create namenode and datanode using the yml file and define the services
```shell
oc create -f namenode.yml
oc create service loadbalancer hdfs-namenode --tcp=8020:8020 --tcp=50070:50070
oc expose service hdfs-namenode --port=50070
oc create -f datanode.yml
```

till now we should have everything ready:
```shell
[root@master1 ~]# oc get pod
NAME              READY     STATUS    RESTARTS   AGE
hdfs-datanode-0   1/1       Running   0          2m
hdfs-namenode-0   1/1       Running   0          18m
[root@master1 ~]# oc get sts
NAME            DESIRED   CURRENT   AGE
hdfs-datanode   1         1         3m
hdfs-namenode   1         1         23m
```

a route to namenode has been created by oc expose service command we run before, and we are able to get a url for it
configuration your /etc/hosts file to redirect hdfs-namenode-hadoop.apps.ocp37.com to your node ip which is hosting your haproxy router
```shell
[root@master1 ~]# oc get route
NAME            HOST/PORT                             PATH      SERVICES        PORT      TERMINATION   WILDCARD
hdfs-namenode   hdfs-namenode-hadoop.apps.ocp37.com             hdfs-namenode   50070                   None
```

use your browser to open  hdfs-namenode-hadoop.apps.ocp37.com   you should able to get the namenode management page as below

https://github.com/hydracz/hadoop-openshift/blob/master/namenode.png

Start my openshift-hadoop trip.  and Good luck

