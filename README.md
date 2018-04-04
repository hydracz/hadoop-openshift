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
lable the node for pod to be schedulable
```shell
oc lable node node1.ocp37.com node-role.kubernetes.io/compute=true 
oc lable node node1.ocp37.com node-role.kubernetes.io/compute=true 
```
