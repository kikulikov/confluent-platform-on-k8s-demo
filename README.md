# TODO

https://github.com/kubernetes-sigs/krew/
https://github.com/ahmetb/kubectx

```sh
brew update && brew upgrade
brew install helm kind kubectl krew kubectx

# Create the K8S cluster
kind create cluster --name confluent
kubectl cluster-info --context kind-confluent

# Switch the context
kubectl config get-contexts
kubectl config use-context kind-confluent
kubectl config current-context

# Switch the namespace
kubectl get namespaces
kubectl create namespace demo
kubectl config set-context --current --namespace=demo
```

## Deploy CFK

https://docs.confluent.io/operator/current/co-deploy-cfk.html
https://docs.confluent.io/operator/current/co-configure-overview.html#confluent-plugin

```sh
helm repo add confluentinc https://packages.confluent.io/helm
helm repo update
helm upgrade --install confluent-operator confluentinc/confluent-for-kubernetes --namespace demo --set kRaftEnabled=true

curl -O https://confluent-for-kubernetes.s3-us-west-1.amazonaws.com/confluent-for-kubernetes-2.8.2.tar.gz
tar -xvf confluent-for-kubernetes-2.8.2.tar.gz

# Install Confluent plugin using Krew
kubectl krew update
kubectl krew uninstall confluent
kubectl krew install --manifest=confluent-for-kubernetes-2.8.2-20240412/kubectl-plugin/confluent-platform.yaml \
--archive=confluent-for-kubernetes-2.8.2-20240412/kubectl-plugin/kubectl-confluent-darwin-arm64.tar.gz

# To get a list of the Confluent Platform CRDs
kubectl api-resources --api-group=platform.confluent.io

# Configure and deploy CRD
kubectl apply -f confluent-platform-kraft.yaml -n demo

```

## Deploying the Dashboard UI

https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

The Dashboard UI is not deployed by default. To deploy it, run the following command:

```sh
# Add kubernetes-dashboard repository
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/

# Deploy a Helm Release named "kubernetes-dashboard" using the kubernetes-dashboard chart
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard

kubectl apply -f dashboard-adminuser.yaml
kubectl -n kubernetes-dashboard create token admin-user

kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy 8443:443
```

## Deploying Apache Flink Operator

https://github.com/apache/flink-kubernetes-operator
https://downloads.apache.org/flink/

```sh
# Install the certificate manager on your Kubernetes cluster to enable adding the webhook component
kubectl create -f https://github.com/jetstack/cert-manager/releases/download/v1.8.2/cert-manager.yaml

# Now you can deploy the selected stable Flink Kubernetes Operator version using the included Helm chart
helm repo add flink-operator-repo https://downloads.apache.org/flink/flink-kubernetes-operator-1.8.0/
helm install flink-kubernetes-operator flink-operator-repo/flink-kubernetes-operator

```

## Flink SQL Client

https://nightlies.apache.org/flink/flink-docs-master/docs/dev/table/sqlclient/
https://blog.rockthejvm.com/flink-sql-introduction/

```sh
kubectl create -f flink-sql-example.yaml

kubectl exec -i pod/basic-example-6d88f6c8f5-r87jf -- /opt/flink/bin/sql-client.sh
```


```sh
# shutdown
kind delete cluster --name confluent
```


