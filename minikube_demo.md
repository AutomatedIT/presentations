
# cleanup - remove any previous cluster & settings
minikube delete; rm -rf ~/.minikube

delete VM - selecting to remove "all files"

# installer links - setup VirtalBox, Minikube and kubectl
VirtualBox: https://www.virtualbox.org/wiki/Downloads

Minikube: https://kubernetes.io/docs/tasks/tools/install-minikube/

#curl -Lo minikube https://storage.googleapis.com/minikube/releases/v1.1.0/minikube-linux-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube

kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl/

#curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.14.0/bin/linux/amd64/kubectl

#chmod +x ./kubectl

#sudo mv ./kubectl /usr/local/bin/kubectl"

# create first cluster - create a cluster, takes a few mins, then check things out
minikube start

minikube ssh

(inside vm)

docker images

docker ps

(outside of VM)

kubectl cluster-info

kubectl get nodes

kubectl describe nodes


# minikube addons - show some of the ways minkube makes things easier for local dev
ls -al ~/.minikube; ls -al ~/.kube

minikube addons list

minikube addons enable heapster

minikube addons enable metrics-server

minikube config view

kubectl --namespace kube-system get pods

minikube addons enable dashboard

minikube addons list


# minikube docker-env - setup local docker client to use minikube docker host
minikube docker-env

eval $(minikube docker-env)

docker ps

docker ps | grep -i metrics

docker logs -f <container>


# miniukbe k8s dashboard - start k8s dashboard and take look around - uses metrics & heapster
minikube dashboard

-take a look at Default namespace > Nodes
 
-take a look at kube-system namespace > Deployments
 
-take a look at kube-system namespace > Pods

-take a look at Storage Classes


# kubectl command line - look at kubectl and keep an eye on things
kubectl get deployment -n kube-system

kubectl get pods -o wide -n kube-system

kubectl get services


# EXAMPLE BASIC app – Initial Deployment of nginx docker image
kubectl run hello-nginx --image=nginx --port=80

kubectl get pods -o wide

kubectl expose deployment hello-nginx --type=NodePort

minikube service --url=true hello-nginx

kubectl get svc


# EXAMPLE BASIC app – Scaling it up to 3 and take a look
kubectl get deployment

kubectl get pods -o wide

kubectl scale --replicas=3 deployment/hello-nginx

kubectl get deployment

kubectl get pods -o wide


# Helm & Tiller setup - show how it's done, but not updating mine
-show:

#curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh

#chmod 700 get_helm.sh

./get_helm.sh


# Tiller Cluster Prep and Install - add pre-reqs for tiller, deploy via helm and take a look
kubectl create serviceaccount -n kube-system tiller

kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

helm init --service-account tiller

kubectl --namespace kube-system get pods


# Helm Charts - look at the list of available Charts, deploy a couple
-show:

https://github.com/helm/charts

helm repo update

helm install stable/redis

helm install stable/mysql


# Helm Charts part deux - watch Helm charts deploy & startup
watch kubectl get pods -o wide

helm ls

helm


# Bonus demo - deploy Wordpress w/MariaDB
helm ls

helm delete <previous things>

helm install --set serviceType=NodePort --name wp-k8s stable/wordpress

watch kubectl get pods -o wide

minikube service --url=true wp-k8s-wordpress

-use https and accept warning...

echo Password: $(kubectl get secret wp-k8s-wordpress -o
jsonpath="{.data.wordpress-password}" | base64 --decode)

# Bonus Bonus Demo - Jenkins too
helm install --set serviceType=NodePort --name jenki stable/jenkins

watch kubectl get pods -o wide

minikube service --url=true jenki-jenkins

-password in UI from Pods > Jenki

-user: admin
