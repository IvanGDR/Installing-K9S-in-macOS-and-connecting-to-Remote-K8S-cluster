# Install K9S in MacOS and Connect to Remote Cluster


Resources:

[K9s - Manage Your Kubernetes Clusters In Style](https://k9scli.io/)

[GitHub - derailed/k9s: 🐶 Kubernetes CLI To Manage Your Clusters In Style!](https://github.com/derailed/k9s)

[Configure local kubectl to access remote Kubernetes cluster - DEV Community](https://dev.to/gvelrajan/configure-local-kubectl-to-remote-access-kubernetes-cluster-2g81)


[k9s - Best Kubernetes CLI To Manage Your Clusters In Style \| ComputingForGeeks](https://computingforgeeks.com/k9s-kubernetes-cli-to-manage-clusters-in-style/)

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
    certificate-authority-data: LS0tLS1CRU==
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
    client-certificate-data: LS0tLS1CRU==
    client-key-data: LS0tLS1CRUdJ==
```

In your local machine, where K9S has been installed.

Create a directory call ".kube" in home directory

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

Firs locate where the K9S configuration files are located:
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
Logs:            /var/folders/hj/_9d883kn69vbj3yw20w610z40000gp/T/k9s-ivan.garciadelrisco.log
Screen Dumps:    /var/folders/hj/_9d883kn69vbj3yw20w610z40000gp/T/k9s-screens-ivan.garciadelrisco
```

Download skin yml file from repository, go to [k9s/skins at master · derailed/k9s · GitHub](https://github.com/derailed/k9s/tree/master/skins)

Select the .yml file skin of your choice. Download or copy/paste the content (note the "\" scape notation) within:

```
cd /Users/ivan/Library/Application\ Support/k9s
```
```
$ ~/Library/Application Support/k9s> ls -la

drwxr-xr-x   4 ivan  staff   128 Jul  9 11:35 .
drwx------+ 55 ivan  staff  1760 Jul  9 07:18 ..
-rw-r--r--   1 ivan  staff   967 Jul  9 11:47 config.yml
-rw-r--r--   1 ivan  staff  2552 Jul  9 11:35 skin.yml
```

Finally, launch K9S:
```
$ k9s
```
&nbsp;
<p align="center">
<img width="800" height="300" src="https://user-images.githubusercontent.com/67383481/178104204-a5939cf1-1913-4097-b264-96434e2b374c.png">
</p>
&nbsp;

