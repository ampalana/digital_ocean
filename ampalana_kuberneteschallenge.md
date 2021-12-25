## Challenge

Step 1: Install `doctl` - DigitalOcean CLI tool

Step: 2: Create a Kubernetes cluster

```
doctl kubernetes cluster create do-k8s-chlng
```
>OUTPUT

```
PS C:\> doctl kubernetes cluster create do-k8s-chlng
Notice: Cluster is provisioning, waiting for cluster to be running
........................................................................
Notice: Cluster created, fetching credentials
Notice: Adding cluster credentials to kubeconfig file found in "C:\\Users\\Jagan/.kube/config"
Notice: Setting current-context to do-nyc1-do-k8s-chlng
ID                                      Name            Region    Version        Auto Upgrade    Status     Node Pools
66004701-3294-4599-b2eb-811f391fenej    do-k8s-chlng    nyc1      1.21.5-do.0    false           running    do-k8s-chlng-default-pool

```

Step 3: Harbor installation steps

Our challenge starts here. I'm using Bitnami [helm chart](https://bitnami.com/stack/harbor/helm) to deploy Harbor

Add bitnami repo to Helm

```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
> OUTPUT
```
PS C:\LAB\harbor> helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```

Before installing helm chart we need to edit the yaml file, so create a yaml file 
```
helm show values bitnami/harbor > harbor-values.yaml
```

open `harbor-values.yaml` in a editor

change the exterlname URL value to `externalURL: https://hub.jagan-sekaran.me` 

set admin password `harborAdminPassword: "<YOUR PASSWORD>"` and commaonName `commonName: 'hub.jagan-sekaran.me'`


Install Harbor via helm
```
helm install harbor bitnami/harbor --values harbor-values.yaml -n harbor --create-namespace
```
>OUTPUT
```PS
PS C:\LAB\harbor> helm install harbor bitnami/harbor --values harbor-values.yaml -n harbor --create-namespace
NAME: harbor
LAST DEPLOYED: Mon Dec 25 15:05:42 2021
NAMESPACE: harbor
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: harbor
CHART VERSION: 11.1.5
APP VERSION: 2.4.0

** Please be patient while the chart is being deployed **

1. Get the Harbor URL:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace harbor -w harbor'
  export SERVICE_IP=$(kubectl get svc --namespace harbor harbor --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo "Harbor URL: http://$SERVICE_IP/"

2. Login with the following credentials to see your Harbor application

  echo Username: "admin"
  echo Password: $(kubectl get secret --namespace harbor harbor-core-envvars -o jsonpath="{.data.HARBOR_ADMIN_PASSWORD}" | base64 --decode)
```

Check pods status - (everything is running now)

```PS
PS C:\LAB\harbor> kubectl get pod -n harbor
NAME                                    READY   STATUS    RESTARTS   AGE
harbor-chartmuseum-7fcffccd47-pftzv     1/1     Running   0          3m49s
harbor-core-85d8cf769d-8ngl6            1/1     Running   4          3m49s
harbor-jobservice-d79dc7b5b-4w5xt       1/1     Running   6          3m49s
harbor-nginx-8679695b9d-gs88w           1/1     Running   0          3m49s
harbor-notary-server-79bd9949d9-mk6l6   1/1     Running   0          3m49s
harbor-notary-signer-6f888ccbd-qr725    1/1     Running   0          3m49s
harbor-portal-58dfcc667d-jspp4          1/1     Running   0          3m49s
harbor-postgresql-0                     1/1     Running   0          3m49s
harbor-redis-master-0                   0/1     Pending   0          3m49s
harbor-registry-676f8ff5d6-lrz99        2/2     Running   0          3m49s
harbor-trivy-0                          1/1     Running   0          3m49s
```

Get External-IP of Harbor loadbalancer (from Portal)


use the external IP to login to Harbor
- UserName: admin
- Password: <PASSWORD_entered_on_yaml>

