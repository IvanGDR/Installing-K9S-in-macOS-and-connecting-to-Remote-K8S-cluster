# Install K9S in MacOS and Connect to Remote K8S Cluster + kubectl, kubectx, kubens and popeye


Resources:

https://k9scli.io/

https://github.com/derailed/k9s

&nbsp;
# Steps:

Install K9s in MacOs (not kubectl installed) via Homebrew
```
$ brew install derailed/k9s/k9s
```

Assuming your remote K8s Cluster is set up, go to the control plane node and to the following directory:
```
$ cd ~/.kube
```
then get the "config" file
```
$ cat config
```
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0xxx==
    server: https://110.11.33.147:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0xxx==
    client-key-data: LS0xxx==
```

In your local machine, where K9S has been installed, create a directory call ".kube" in home directory

```
$ sudo mkdir .kube
```

within this directory create a file named "config" and paste the content of the remote "config" file found on the K8S control plane node 
```
$ cat config
```
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0xxx==
    server: https://110.11.33.147:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0xxx==
    client-key-data: LS0xxx==
```

# Adding a skin to K9s

First find where the K9S configuration files are located:
```
$ k9s info                                 
```
```
 ____  __.________
|    |/ _/   __   \______
|      < \____    /  ___/
|    |  \   /    /\___ \
|____|__ \ /____//____  >
        \/            \/

Configuration:   /Users/ivan/Library/Application Support/k9s/config.yml
Logs:            /var/folders/hj/_9d883kn69vbj3yw20w610z40000gp/T/k9s-ivan.log
Screen Dumps:    /var/folders/hj/_9d883kn69vbj3yw20w610z40000gp/T/k9s-screens-ivan
```

Download skin .yml file from repository, go to https://github.com/derailed/k9s/tree/master/skins

Select the .yml file skin of your choice. Download or copy/paste the content (note the "\\" scape notation) within:

```
$ cd /Users/ivan/Library/Application\ Support/k9s
```
```
$ ~/Library/Application Support/k9s> ls -la

-rw-r--r--   1 ivan  ivan   967 Jul  9 11:47 config.yml
-rw-r--r--   1 ivan  ivan  2552 Jul  9 11:35 skin.yml
```
The config.yml above is part of the K9S installation. Save your skin using "skin.yml".
&nbsp;

Finally, launch K9S:
```
$ k9s
```
&nbsp;
<p align="center">
<img width="800" height="250" src="https://user-images.githubusercontent.com/67383481/178104204-a5939cf1-1913-4097-b264-96434e2b374c.png">
</p>
&nbsp;

# Extra Tools - kubectl client managing multi K8S clusters, kubectx+kubens and popeye

## kubectl
https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/

```
$ brew install kubectl
```
```
$ kubectl version                       
```
```
Client Version: version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.2", GitCommit:"f66044f4361b9f1f96f0053dd46cb7dce5e990a8", GitTreeState:"clean", BuildDate:"2022-06-15T14:14:10Z", GoVersion:"go1.18.3", Compiler:"gc", Platform:"darwin/amd64"}
Kustomize Version: v4.5.4
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.2", GitCommit:"9d142434e3af351a628bffee3939e64c681afa4d", GitTreeState:"clean", BuildDate:"2022-01-19T17:29:16Z", GoVersion:"go1.17.5", Compiler:"gc", Platform:"linux/amd64"}
```

> kubectl should recognise the cluster set up previously in our K9s installation part

```
$ kubectl get pods -n kube-system
```
```
NAME                                                             READY   STATUS    RESTARTS   AGE
calico-kube-controllers-7bc6547ffb-k76sw                         1/1     Running   0          5d15h
calico-node-28hqv                                                1/1     Running   0          5d22h
calico-node-49t4r                                                1/1     Running   0          5d22h
calico-node-gmsdb                                                1/1     Running   0          5d22h
coredns-64897985d-fcx7k                                          1/1     Running   0          5d15h
coredns-64897985d-zbmj7                                          1/1     Running   0          5d15h
etcd-ip-10-101-33-147.srv101.dsinternal.org                      1/1     Running   0          5d22h
kube-apiserver-ip-10-101-33-147.srv101.dsinternal.org            1/1     Running   0          5d16h
kube-controller-manager-ip-10-101-33-147.srv101.dsinternal.org   1/1     Running   0          5d16h
kube-proxy-6crbh                                                 1/1     Running   0          5d16h
kube-proxy-bq5v7                                                 1/1     Running   0          5d16h
kube-proxy-kftjr                                                 1/1     Running   0          5d16h
kube-scheduler-ip-10-101-33-147.srv101.dsinternal.org            1/1     Running   0          5d16h
```

furthermore:
```
$ kubectl config get-contexts 
```
```
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin
```

&nbsp;
### Another kubectl handy commands for configuring multi clusters access using KUBECONFIG variable

considering the following:
```
$~/.kube/ ls -la
```
```
-rw — — — — 1 ivan ivan 230 Jul 10 20:19 config
-rw — — — — 1 ivan ivan 136 Jul 10 20:20 config_2
-rw — — — — 1 ivan ivan 181 Jul 10 20:20 config_3
```

Exporting KUBECONFIG

```
$ export KUBECONFIG="${HOME}/.kube/config"
```

Exporting more than one config file at once

```
$ export KUBECONFIG="${HOME}/.kube/config:${HOME}/.kube/config_2"
```

```
$ kubectl config get-contexts
```
```
CURRENT   NAME                          CLUSTER       AUTHINFO           NAMESPACE
          dev-ramp-up                   development   developer          ramp
*         kubernetes-admin@kubernetes   kubernetes    kubernetes-admin
```

To merge kubeconfig files in a view
```
$ kubectl config view               
```
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://10.101.33.147:6443
  name: kubernetes
contexts:
- context:
    cluster: development
    namespace: ramp
    user: developer
  name: dev-ramp-up
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```

to append a new cluster configuration file to an already KUBECONFIG variable in place

```
$ export KUBECONFIG="${KUBECONFIG}:${HOME}/.kube/config_3"
```

To check KUBECONFIG variable

```
$ echo $KUBECONFIG
```
```
/Users/ivan/.kube/config:/Users/ivan/.kube/config_2:/Users/ivan/.kube/config_3
```

to clear KUBECONFIG variable

```
$ unset KUBECONFIG
```

Changing context to "dev-ramp-up" using kubectl
```
$ kubectl config use-context dev-ramp-up
```

Changing default namespace using kubectl
```
$ kubectl config set-context --current --namespace=<namespace name>
```

## kubectx + kubens
The following will install kubectx and kubens
```
$ brew install kubectx
```
```
$ kubectx
```
```
dev-ramp-up
kubernetes-admin@kubernetes
```

Change context using kubectx
```
$ kubectx <context name>
```

Working with namespaces
```
$ kubens
```
```
default
dev
kube-node-lease
kube-public
kube-system
testnamespace
``````

Change namespaces using kubens
```
$ kubens <namespace name>
```

## popeye

https://github.com/derailed/popeye

```
brew install derailed/popeye/popeye
```

```
$ popeye
```

&nbsp;
<p align="center">
<img width="800" height="250" src="https://user-images.githubusercontent.com/67383481/178202683-8785b674-3cf2-4c04-a411-70fb3a073877.png">
</p>
&nbsp;
