# Install Kong on Kubernetes Cluster locally

This guide will walk you through the steps to install Kong on a local Kubernetes cluster using Helm chart.

## Prerequisites

Before you begin, make sure you have the following prerequisites installed:

 - Kubernetes cluster (e.g., Minikube, Docker Desktop with Kubernetes enabled)
 - Helm (v3 or later)

### Step 1: Add Kong Helm Repo
```
To install Kong using Helm, you need to add the Kong Helm repository to your Helm configuration. Run the following command:

Add the repo on your machine:
helm repo add kong https://charts.konghq.com
helm repo update

Use helm repo list command to view the added repo.

helm repo list

And Helm Search to view the charts under kong repo.

helm search repo kong

There are two available charts.
kong/ingress
kong/kong</sub>
```
### Step 2: Installing Kong from Helm chart
```
Before installing directly from the chart kong/kong. First export the default chart to an file values-default.yaml.

helm show values kong/kong > values-default.yaml

This should create an file named values-default.yaml in the current directory and export details.

Then use helm install command to install Kong API Gateway with kong as an release name and delpoy in namespace "kong" override with the --file "values-default.yaml"

helm install kong -n kong kong/kong -f values-default.yaml</sub>
```
### Verify Pod should be in ready state.
```
kubectl get all -n kong
NAME                           READY   STATUS    RESTARTS     AGE
pod/kong-kong-58bb4bdc-rmjwf   2/2     Running   1 (2s ago)   10s

NAME                                   TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
service/kong-kong-manager              NodePort       10.100.175.101   <none>        8002:32278/TCP,8445:32743/TCP   10s
service/kong-kong-proxy                LoadBalancer   10.104.153.238   192.168.2.6   80:32293/TCP,443:31503/TCP      10s
service/kong-kong-validation-webhook   ClusterIP      10.110.186.3     <none>        443/TCP                         10s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/kong-kong   1/1     1            0           10s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/kong-kong-58bb4bdc   1         1         0       10s

From the Configuration file you can Enable or Disable Kong features or plugin etc.

Example here we updated image to use "kong/kong-gateway" from "kong"

image:
  repository: **kong/kong-gateway**
  tag: "3.6"

And modified admin service from NodePort to LoadBalancer type in the configuartion and also you can disable if don't want to expose the admin API.

admin:
  enabled: true  
  type: **LoadBalancer**
  loadBalancerClass:

Once modified the changes in the custom file run the Helm Upgrade command.

helm upgrade kong -n kong kong/kong -f values-default.yaml

Helm list command used to view the revision status.

helm list -n kong
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
kong    kong            7               2024-06-01 22:43:28.998620176 +0530 IST deployed        kong-2.38.0     3.6</sub>
```
### Step 3: Deploy few sample applications with kong ingress. You can find the application code under "dummy_app" folder.
```
kubectl apply -f bar.yaml
kubectl apply -f bar-ingress.yaml

kubectl apply -f foo.yaml
kubectl apply -f foo-ingress.yaml

kubectl apply -f echo.yaml
kubectl apply -f echo-ingress.yaml

#### Step 4: Test accessing the application using the Kong API address

http://{Kong Proxy IP}/path # Path based Ingress
http://<Kong Proxy IP>/bar
http://<Kong Proxy IP>/foo

http://{Echo App Ingress HostName} # Host based Ingress
```
![image](https://github.com/sunilc18/Kong-Kubernetes/assets/40242624/330234eb-5d24-4e53-802a-af6582a07b6c)


