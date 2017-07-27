# hyperledger-composer-ocp
Hyperledger Composer on OpenShift Container Platform [WIP]


### Environment
```
$ oc version

oc v3.5.0.17+c55cf2b
kubernetes v1.5.2+43a9be4
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://ip-xxx-xx-xx-xxx.us-west-x.compute.internal:8443
openshift v3.5.0.17+c55cf2b
kubernetes v1.5.2+43a9be4
```

```
$ cat /etc/redhat-release 

Red Hat Enterprise Linux Server release 7.3 (Maipo)
```

### Project setup

```
$ oc new-project hlcomposer
```


Both the peer and member service images will require root access.
Therefore we need to add the default service account to the *anyuid* SCC.
```
$ oc adm policy add-scc-to-user anyuid -z default
```


One way to verify the deployment through the web console is to give
the *admin* role to a user. In this case, the user `dev` will be able to access 
the hlcomposer project. 
```
$ oadm policy add-role-to-user admin dev -n hlcomposer
```

```
$ git clone https://github.com/rflorenc/hyperledger-composer-ocp.git
$ cd hyperledger-composer-ocp/resources/hlfv0.6/

$ for resource in *.yaml;do oc create -f $resource; done

deploymentconfig "composer" created
service "composer" created
deploymentconfig "membersrvc" created
service "membersrvc" created
deploymentconfig "vp0" created
service "vp0" created
```


Verify installation with:

``` 
$ oc get all 

NAME            REVISION   DESIRED   CURRENT   TRIGGERED BY
dc/composer     1          1         1         config
dc/membersrvc   1          1         1         config
dc/vp0          1          1         1         config

NAME              DESIRED   CURRENT   READY     AGE
rc/composer-1     1         1         1         31s
rc/membersrvc-1   1         1         1         31s
rc/vp0-1          1         1         1         30s

NAME             CLUSTER-IP       EXTERNAL-IP   PORT(S)                               AGE
svc/composer     172.26.215.127   <none>        8080/TCP                              31s
svc/membersrvc   172.27.181.49    <none>        7054/TCP                              30s
svc/vp0          172.26.81.237    <none>        7050/TCP,7051/TCP,7052/TCP,7053/TCP   30s

NAME                    READY     STATUS    RESTARTS   AGE
po/composer-1-lg6xz     1/1       Running   0          28s
po/membersrvc-1-fw4m8   1/1       Running   0          27s
po/vp0-1-961x2          1/1       Running   0          27s

```
