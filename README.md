# chaos-helm
This repo consists of helm charts for the chaos experiment custom resources. The helm installation is typically used in [admin mode](https://docs.litmuschaos.io/docs/admin-mode/) of litmus, wherein all chaosexperiment CRs (and other chaos resources such as chaosengine)are placed/run in a single namespace while the applications themselves are in their respective namespaces. 

## PreRequisites

- Install [helm3](https://helm.sh/docs/intro/install/)  

**Note**: Litmus uses Kubernetes custom resource definitions (CRDs) to define chaos intent. Helm3 handles CRDs [better](https://helm.sh/docs/chart_best_practices/custom_resource_definitions/#helm) than helm2!

- While the order of installation is not strict, you also need to install the Litmus Chaos Operator helm chart to be able to
  run these experiments. Refer to these [instructions](https://github.com/litmuschaos/litmus-helm/blob/master/README.md) to achieve 
  the same. 

## Installation Steps 

The following steps will help you install the generic Kubernetes chaos experiment custom resources via helm.

### Step-1: Add the chaos helm repository

```
root@demo:~# helm repo add k8s-chaos https://litmuschaos.github.io/chaos-helm/
"k8s-chaos" has been added to your repositories
```

```
root@demo:~# helm repo list
NAME            URL                                       
k8s-chaos     https://litmuschaos.github.io/chaos-helm/
```

```
root@demo:~# helm search repo k8s-chaos
NAME                            CHART VERSION   APP VERSION     DESCRIPTION
k8s-chaos/kubernetes-chaos      1.2.0           1.2.0           A Helm chart to install litmus chaos experiment...
```

### Step-2: Create the litmus namespace 

- The litmus chaos experiment CRs will be placed in this namespace. 

**Note**: The chaos experiments can be placed in any namespace, though it is typically placed in "litmus". 
 Please note that the chaosengine will have to be created in the same namespace. 

```
root@demo:~# kubectl create ns litmus 
namespace/litmus created
```

### Step-3: Install the Kubernetes chaos experiments

```
root@demo:~# helm install k8s k8s-chaos/kubernetes-chaos --namespace=litmus
NAME: k8s
LAST DEPLOYED: Fri Apr  3 17:44:39 2020
NAMESPACE: litmus
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## Additional Steps (Verification)

You can run the following commands if you wish to verify if all chaos experiments are installed successfully.

- Check if chaos experiments are named appropriately and available in litmus namespace

```
root@demo:~# kubectl get chaosexperiments -n litmus
NAME                         AGE
k8s-container-kill           13s
k8s-disk-fill                13s
k8s-disk-loss                13s
k8s-node-cpu-hog             13s
k8s-node-drain               13s
k8s-node-memory-hog          13s
k8s-pod-cpu-hog              13s
k8s-pod-delete               13s
k8s-pod-network-corruption   13s
k8s-pod-network-latency      13s
k8s-pod-network-loss         13s

```

- Check if the chaos-admin RBAC components are installed successfully 

```
root@demo:~# kubectl get serviceaccount -n litmus
NAME              SECRETS   AGE
default           1         11h
k8s-chaos-admin   1         90s
```

- Check if the chaos operator RBAC is configured appropriately: 

```
root@demo:~# kubectl get clusterrole | grep chaos-admin
k8s-chaos-admin                                        71s
```

```
root@demo:~# kubectl get clusterrolebinding | grep chaos-admin
k8s-chaos-admin                                        98s
```

