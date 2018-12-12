# spark-operator
ConfigMap-based approach for managing the Spark clusters in Kubernetes and OpenShift.

# Installation
```
helm install incubator/spark-operator
```

or 

```
helm install --set operator.env.crd=true incubator/spark-operator
```

The operator needs to create Service Account, Role and Role Binding. If running in Minikube, you may need to
start it this way:

```
minikube start --vm-driver kvm2 --bootstrapper kubeadm --kubernetes-version v1.7.10
kubectl -n kube-system create sa tiller
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller
```

# Usage
Create Apache Spark Cluster:

```
cat <<EOF | kubectl create -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-cluster
  labels:
    radanalytics.io/kind: sparkcluster
data:
  config: |-
    workerNodes: "2"
EOF
```

or for CRDs:

```
cat <<EOF | kubectl create -f -
apiVersion: radanalytics.io/v1
kind: sparkcluster
metadata:
  name: my-cluster
spec:
  workerNodes: "2"
EOF
```

For more details consult https://github.com/radanalyticsio/spark-operator/blob/master/README.md
or check the [examples](https://github.com/radanalyticsio/spark-operator/tree/master/examples).
