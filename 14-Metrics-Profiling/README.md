# to actually know what we should specify as our resource request and limit, we need to have an idea of the memory usage of our pods and nodes, we can do that using

kubectl top pod   # to know about the usage of the pods

kubectl top node    # to know about the usage of the nodes

# but metrics are not preinstalled on a kubernetes, you have to enable metric service, which is an addon

we download the addon for metric on our node using

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

```

we can check more details about our nodes using
```
kubectl describe nodes
```

# we have been able to set a request cpu and memory and also limit memory and cpu based on using kubectl top pod

we have been able to use the kubectl top pod , which has help us made good decisions to setup sensible request and limit on our pods



