# In this section, we are learning about HPA ( horizontal Pod Autoscaling)

- next: to get started with HPA, we need a way to keep checking our pods usage, checking there metrics, so we need to have metric-server installed - (kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml)

- next: we can check our pods metric and node metrics using
kubectl top pod
kubectl top node

- next: we need a HPA defintion for the pod we will like to scale in or out


# in the deployment file, we have specify the resource cpy request, e.g 100m , and in the HPA we specify 50% , which means if the cpu usage go above 50m, then the HPA will trigger autoscaling

```
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa-for-api-gateway
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-gateway    # here this name must match with the deployment name for HPA
  minReplicas: 1
  maxReplicas: 3
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50   # the % of the cpu request on the deployment 
```

# to see the HPA
```
kubectl get hpa
```

# here in this example, we have used api-gateway as an example for the HPA, and we were able to increase the CPU load by adding /api/panic at the end of the URL to spike up the CPU usage, which will trigger the HPA and will scale out the pods 