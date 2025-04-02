# Fast-Track | Introdução a Kubernetes
trn-ft-kubernetes-ini

### terraform
```shell
brew tap hashicorp/tap

terraform init
terraform plan -var "client_id=${ARM_CLIENT_ID}" -var "client_secret=${ARM_CLIENT_SECRET}" -var "tenant_id=${ARM_TENANT_ID}" -var "subscription_id=${ARM_SUBSCRIPTION_ID}" -var "ssh_public_key_data=${SSH_PUBLIC_KEY_DATA}"
```

### minikube
```shell
brew install minikube
minikube start --driver=docker

kubectl config get-contexts
kubectl config set-context minikube

kubectl get nodes
kubectl get pods --all-namespaces

minikube dashboard

minikube addons list
minikube stop
```

### gcloud [gke]
```shell
gcloud config set project silver-charmer-243611

gcloud services enable container.googleapis.com
gcloud config set compute/zone us-central1-c

gcloud container clusters --help
gcloud container clusters create "gke-owshq-orion-dev" --num-nodes=2

gcloud container clusters get-credentials gke-owshq-orion-dev
kubectl get nodes
```


### install kubectl
```shell
brew install kubectl
kubectl --help
```

![kubectl](images/kubectl.png)

### interact with kubectl
```shell
kubectl config get-contexts
kubectl config set-context orn-polaris-dev

k get nodes -o wide
k top node
k describe node 

k get namespaces
k get events


k api-resources
k get pods,services
k explain pods
k get pods --all-namespaces
k top pod

k describe pod 
k get pvc 

k get deployments
```

### interact in a [imperative] way
```shell
k delete namespace app

k create namespace app
k config set-context orn-polaris-dev --namespace app

k run frontend --image=nginx:1.24.0 --port=80
k exec -it frontend -- /bin/bash
k edit pod frontend
k patch pod frontend -p '{"spec":{"containers":[{"name":"frontend","image":"nginx:1.25.1"}]}}'
k logs frontend
k delete pod frontend
```

### interact in a [declarative] way
```shell
k apply -f nginx-deployment.yaml
k get deployment nginx-deployment
k get deployment nginx-deployment -o yaml
k delete -f nginx-deployment.yaml
```

### interact in a [hybrid] way
```shell
k run frontend --image=nginx --dry-run=client -o yaml
k run frontend --image=nginx:1.25.1 --port=80 -o yaml --dry-run=client > nginx-pod.yaml
vi nginx-pod.yaml
k apply -f nginx-pod.yaml
k get pods 
k describe pod frontend
k delete -f nginx-pod.yaml
```

### liveness probe
```shell
k apply -f kuard-pod-liveness-health.yaml
k get pods

k port-forward kuard 8080:8080
http://localhost:8080

k describe pods kuard
k events

k delete -f kuard-pod-liveness-health.yaml
```

### readiness probe
```shell
k apply -f nginx-pod-readiness-health.yaml
k get pods

k describe pods nginx
k events

k delete -f nginx-pod-readiness-health.yaml
```

### resource management with [request] & [limits]
```sh
k apply -f kuard-pod-reslim.yaml
k get pods

k describe pods kuard
k top pods
k describe node 

k delete -f kuard-pod-reslim.yaml
```

### volume
```sh
k apply -f kuard-pod-vol.yaml
k get pods

k describe pods kuard
k exec -it kuard -- /bin/sh

k delete -f kuard-pod-vol.yaml
```

### labels, selectors & annotations
```sh
k apply -f nginx-pod-labels.yaml
k get pods

k describe pods nginx
kubectl get pods -l environment=production,tier=frontend

k get nodes -o wide
k describe node aks-agentpool-13974181-vmss000000

k describe pod airflow-worker-0 --namespace orchestrator
k describe svc airflow-webserver --namespace orchestrator
k get pods -l component=webserver,release=airflow,tier=airflow

k delete -f nginx-pod-labels.yaml
```

### pod-controller: [daemonset] spec
```shell
k apply -f fluentd-elasticsearch-daemonset.yaml
k get pods --namespace kube-system

k get daemonset --namespace kube-system
k describe daemonset fluentd-elasticsearch --namespace kube-system

k get pods --namespace kube-system
k describe pod fluentd-elasticsearch-4st2h --namespace kube-system
k delete pod fluentd-elasticsearch-4st2h --namespace kube-system

k get pod --namespace kube-system --watch

k delete -f fluentd-elasticsearch-daemonset.yaml
```

### pod-controller: [replicaset] spec
```shell
k apply -f frontend-replicaset.yaml --namespace default
k get pods --namespace default

k get rs --namespace default
k describe rs frontend --namespace default

k get pod --namespace default --watch
k delete pod frontend-vqfzz --namespace default

k delete -f frontend-replicaset.yaml --namespace default
```

### pod-controller: [deployment] spec
```shell
k apply -f nginx-deployment.yaml --namespace default
k get pods --namespace default

k get deployment --namespace default
k describe deployment nginx-deployment --namespace default

k get pod --namespace default --watch
k delete pod nginx-deployment-cbdccf466-nnj4r --namespace default

k delete -f nginx-deployment.yaml --namespace default
```

### pod-controller: [statefulset] spec
```shell
k apply -f nginx-statefulset.yaml --namespace default
k get pods --namespace default

k get statefulset --namespace default
k describe statefulset web --namespace default

k get svc --namespace default

k get pvc --namespace default
k get pv --namespace default
k get sc
describe sc default

k delete pod web-1 --namespace default

k delete -f nginx-statefulset.yaml --namespace default
```

### pod-controller: [job] spec
```shell
k apply -f job.yaml --namespace default

k get jobs --namespace default
k get job pi -o yaml --namespace default
k get pods --namespace default

k logs pi-trsqh

k delete -f job.yaml --namespace default
```

### pod-controller: [cronjob] spec
```shell
k apply -f cronjob.yaml --namespace default

k get cronjob --namespace default
k get cronjob busybox-print-date -o yaml --namespace default

k get pods --namespace default
k logs busybox-print-date-28557314-srwdz

k delete -f cronjob.yaml --namespace default
```

### configmap 
```sh
k apply -f configmap.yaml --namespace default

k get configmap game-demo -o yaml --namespace default
k describe pod configmap-demo-pod --namespace default

k delete -f configmap.yaml --namespace default
```

### secret 
```sh
echo -n 'admin' | base64
echo -n 'password123' | base64

k apply -f secret.yaml --namespace default

k get secrets --namespace default
k describe secret sensitive-secret --namespace default
k describe pod nginx-pod --namespace default

k get secret sensitive-secret -o yaml --namespace default
echo 'YWRtaW4=' | base64 --decode
k get secret sensitive-secret -o jsonpath='{.data.username}' | base64 --decode; echo

k delete -f secret.yaml --namespace default
```

### service: [clusterip] spec
```shell
k apply -f svc-clusterip.yaml --namespace default

k describe pod nginx-pod --namespace default
k describe svc nginx-service --namespace default

k get svc --all-namespaces
k get svc mongodb -o yaml --namespace datastore

kubectl port-forward pod/nginx-pod 8080:80
http://localhost:8080/

k delete -f svc-clusterip.yaml --namespace default
```

### service: [nodeport] spec
```shell
k apply -f svc-nodeport.yaml --namespace default

k describe pod nginx-pod --namespace default
k describe svc nginx-nodeport-service --namespace default

k delete -f svc-nodeport.yaml --namespace default
```

### service: [lb] spec
```shell
k apply -f svc-loadbalancer.yaml --namespace default

k describe pod nginx-pod --namespace default
k describe svc nginx-loadbalancer --namespace default

http://4.153.175.243/

k delete -f svc-loadbalancer.yaml --namespace default
```

### service: [ingress] spec
```shell
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-nginx --create-namespace \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.service.externalTrafficPolicy=Local \
    --set controller.service.type=LoadBalancer
    
helm ls --namespace ingress-nginx

k get pods --namespace ingress-nginx
k get deployment --namespace ingress-nginx
k get svc --namespace ingress-nginx

k apply -f svc-ingress.yaml --namespace ingress-nginx

deployment.apps/apple-app created
service/apple-service created
deployment.apps/banana-app created
service/banana-service created
ingress.networking.k8s.io/owshq-ingress created

k get deployment --namespace ingress-nginx
k get pods --namespace ingress-nginx
k get svc --namespace ingress-nginx

k get ingress --namespace ingress-nginx
k describe ingress owshq-ingress --namespace ingress-nginx

k get pods --namespace ingress-nginx
kubectl logs -n ingress-nginx nginx-ingress-ingress-nginx-controller-d776646fd-29xw6

http://4.152.235.41/banana
http://4.152.235.41/apple

k delete -f svc-ingress.yaml --namespace ingress-nginx
helm uninstall nginx-ingress --namespace ingress-nginx
```

### network security policy [nsp]
```shell
k apply -f network-policy.yaml --namespace default

deployment.apps/backend-deployment created
service/backend-service created
deployment.apps/frontend-deployment created
service/frontend-service created
networkpolicy.networking.k8s.io/backend-network-policy created

k get deployment --namespace default
k get pods --namespace default
k get svc --namespace default

k get networkpolicy backend-network-policy -o yaml --namespace default

k get pods --namespace default
k exec -it frontend-deployment-5fdd476746-8zd6g --namespace default -- curl http://backend-service

k run test-pod --image=busybox --restart=Never -- sleep 3600
k exec -it test-pod -- wget -O- http://backend-service

k delete pod test-pod --namespace default
k delete -f network-policy.yaml --namespace default
```

### pvc, pv & sc
```shell
k get statefulset --namespace orchestrator
k describe statefulset airflow-postgresql --namespace orchestrator

k get pods --namespace orchestrator
k describe pod airflow-postgresql-0 --namespace orchestrator

k get pvc --namespace orchestrator
k describe pvc data-airflow-postgresql-0 --namespace orchestrator

k get pv --namespace orchestrator
k describe pv pvc-95759308-6c46-483d-8d10-ce171f6d66a6 --namespace orchestrator

k get sc
k describe sc default

k get nodes
k describe node aks-agentpool-13974181-vmss000000
```

### tools
```shell
brew install kubectx
k config get-contexts
k config set-context orn-polaris-dev

kubectx
kubectx docker-desktop
kubectx orn-polaris-dev

kubens
kubens orchestrator
k get pods

brew install derailed/k9s/k9s
k9s

brew install stern
brew install jq

stern --help
stern --namespace orchestrator .
stern --namespace orchestrator "airflow-worker.*"
stern --namespace orchestrator --timestamps "airflow-worker.*"
stern --namespace orchestrator --tail 100 "airflow-scheduler-.*"
stern --namespace orchestrator "airflow-worker.*" --selector component=worker | grep "ERROR"
stern --all-namespaces -l component=worker
stern airflow-worker-0 -o json | jq .

brew install derailed/popeye/popeye

popeye help
popeye version
popeye
popeye -A -o json | jq
popeye -n orchestrator
```