
---
# This file (minikube_demo.md) is in our GitHub repo:
---
    https://github.com/AutomatedIT/presentations


# Installer links
- setup VirtualBox, Minikube and kubectl

VirtualBox: https://www.virtualbox.org/wiki/Downloads

Minikube: https://kubernetes.io/docs/tasks/tools/install-minikube/

kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl/

`curl -Lo minikube https://storage.googleapis.com/minikube/releases/v1.1.0/minikube-linux-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube`

`curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.14.0/bin/linux/amd64/kubectl`

`chmod +x ./kubectl`

`sudo mv ./kubectl /usr/local/bin/kubectl`

# Cleanup
- if required, remove any previous cluster & settings

`minikube delete; rm -rf ~/.minikube`

delete VM in VirtualBox - selecting to remove "all files"


---

# Create first cluster
- then take a look around in & out of the VM

minikube start # (already done before we started)

minikube ssh

(inside vm)

docker images

docker ps

(outside of VM)

kubectl cluster-info

kubectl get nodes

kubectl describe nodes


# Minikube addons - show some of the ways minkube makes things easier for local dev
ls -al ~/.minikube; ls -al ~/.kube

minikube addons list

minikube addons enable heapster

minikube addons enable metrics-server

minikube config view

kubectl --namespace kube-system get pods

minikube addons enable dashboard

minikube addons list


# Minikube docker-env - setup local docker client to use minikube docker host
minikube docker-env

eval $(minikube docker-env)

docker ps

docker ps | grep -i metrics

docker logs -f <container>


# Miniukbe k8s dashboard - start k8s dashboard and take look around - uses the metrics & heapster deployed earlier
minikube dashboard

-take a look at Default namespace > Nodes


-take a look at kube-system namespace > Deployments


-take a look at kube-system namespace > Pods


# kubectl command line - look at kubectl and keep an eye on things
kubectl get deployment -n kube-system

kubectl get pods -o wide -n kube-system

kubectl get services

kubectl


# Example app – deploy "Hello World" nginx docker image
kubectl run hello-nginx --image=nginx --port=80

kubectl get pods -o wide

kubectl expose deployment hello-nginx --type=NodePort

minikube service --url=true hello-nginx

kubectl get svc


# Example app – scale to x3 replicas and take a look
kubectl get deployment

kubectl get pods -o wide

kubectl scale --replicas=3 deployment/hello-nginx

kubectl get deployment

kubectl get pods -o wide

kubectl scale --replicas=0 deployment/hello-nginx


# Helm & Tiller setup - show how it's done - not updating mine just in case...
-show:

`curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`

`chmod 700 get_helm.sh`

`./get_helm.sh`


# Tiller prep & install - add RBAC for tiller, deploy via helm and take a look
kubectl create serviceaccount -n kube-system tiller

kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

helm init --service-account tiller

kubectl --namespace kube-system get pods


# Helm Charts - look at the list of available Charts, then deploy a couple
-show:

https://github.com/helm/charts

helm repo update

helm install stable/redis

helm install stable/mysql

watch kubectl get pods -o wide


# Helm Charts part deux - watch Helm charts deploy & startup
watch kubectl get pods -o wide

helm ls

helm


# Demo - Jenkins too

`if my laptop can handle it...`

helm ls

helm delete <things>

helm install --set serviceType=NodePort --name jenki stable/jenkins

watch kubectl get pods -o wide

minikube service --url=true jenki-jenkins

-password in UI from Pods > Jenki

-user: admin


# Bonus demo - deploy Wordpress w/MariaDB

`if laptop still working...`

helm ls

helm delete --purge jenki
#give it a while...

kubectl delete pods <jenkinpod> if needed

helm install --set serviceType=NodePort --name wp-k8s stable/wordpress

watch kubectl get pods -o wide

minikube service --url=true wp-k8s-wordpress

-use https and accept warning...

-log in as `user` and...

echo Password: $(kubectl get secret wp-k8s-wordpress -o
jsonpath="{.data.wordpress-password}" | base64 --decode)


---

# Cleanup

minikube delete; rm -rf ~/.minikube
