# spark-operator
ConfigMap-based approach for managing the Spark clusters and apps in Kubernetes and OpenShift.

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
    worker:
      instances: "2"
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
  worker:
    instances: "2"
EOF
```

### Configuration

_The following table lists the configurable parameters of the Spark operator chart and their default values._

| Parameter                    | Description                                                  | Default                                 |
| ---------------------------- | ------------------------------------------------------------ | --------------------------------------- |
| `image.repository`           | The name of the operator image                               | `quay.io/radanalyticsio/spark-operator` |
| `image.tag`                  | The image tag representing the version of the operator       | `latest-released`                       |
| `image.pullPolicy`           | Container image pull policy                                  | `IfNotPresent`                          |
| `env.namespace`              | Kubernetes namespace where Spark operator watch for events. If `*` is used, it watches in all namespaces, if empty string is used, it will watch only in the same namespace the operator is deployed in.   | `""`                                    |
| `env.crd`                    | Whether to use CustomResource or ConfigMap based approach.   | `false`                                 |
| `env.reconciliationInterval` | How often (in seconds) the full reconciliation should be run | `180`                                   |
| `env.metrics`                | Whether to start metrics server to be scraped by Prometheus. | `false`                                 |
| `env.metricsPort`            | The port for the metrics http server                         | `8080`                                  |
| `env.internalJvmMetrics`     | Whether to expose also internal JVM metrics?                 | `false`                                 |
| `env.colors`                 | Colorized log messages                                       | `true`                                  |
| `resources.memory`           | Memory limit for the operator pod (used by K8s scheduler)    | `512Mi`                                 |
| `resources.cpu`              | Cpu limit for the operator pod (used by K8s scheduler)       | `1000m`                                 |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install` or `helm template`.



For more details consult https://github.com/radanalyticsio/spark-operator/blob/master/README.md
or check the [examples](https://github.com/radanalyticsio/spark-operator/tree/master/examples).
