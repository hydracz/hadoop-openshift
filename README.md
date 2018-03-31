# hadoop-openshift
setting up hadoop in my test lab


## Create a Project

```shell
oc new-project hadoop
oc create sa hadoop
oc login -u system:admin
oc adm policy add-scc-to-user anyuid -z hadoop
```
