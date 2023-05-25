## Step-01: Check that you have the IAM permission for Cluster Autoscaler 
- Go to Services -> IAM -> Roles -> eksctl has the node role  


## Step-02: Deploy Cluster Autoscaler
```
# Deploy the Cluster Autoscaler to your cluster

kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml

# Add the cluster-autoscaler.kubernetes.io/safe-to-evict annotation to the deployment

kubectl -n kube-system annotate deployment.apps/cluster-autoscaler cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```
## Step-03: Edit Cluster Autoscaler Deployment to add Cluster name and two more parameters
```
kubectl -n kube-system edit deployment.apps/cluster-autoscaler
```
- **Add cluster name**
```yml
# Before Change
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/<YOUR CLUSTER NAME>

# After Change
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/eksdemo1
```

- **Add two more parameters immeditely under the aboe changes in the yaml file**
```yml
        - --balance-similar-node-groups
        - --skip-nodes-with-system-pods=false
```
- **Sample for reference**
```yml
    spec:
      containers:
      - command:
        - ./cluster-autoscaler
        - --v=4
        - --stderrthreshold=info
        - --cloud-provider=aws
        - --skip-nodes-with-local-storage=false
        - --expander=least-waste
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/eksdemo1
        - --balance-similar-node-groups
        - --skip-nodes-with-system-pods=false
```

## Step-05: Set the Cluster Autoscaler Image related to our current EKS Cluster version
- Open https://github.com/kubernetes/autoscaler/releases
- Find our release version (example: 1.25.1) and update the same. 
- Our Cluster version is 1.25 and our cluster autoscaler version is 1.25.1 as per above releases link 
```
# Template
# Update Cluster Autoscaler Image Version
kubectl -n kube-system set image deployment.apps/cluster-autoscaler cluster-autoscaler=us.gcr.io/k8s-artifacts-prod/autoscaling/cluster-autoscaler:v1.XY.Z


# Update Cluster Autoscaler Image Version
kubectl -n kube-system set image deployment.apps/cluster-autoscaler cluster-autoscaler=us.gcr.io/k8s-artifacts-prod/autoscaling/cluster-autoscaler:v1.25.1
```

## Step-06: Verify Image version got updated
```
kubectl -n kube-system get deployment.apps/cluster-autoscaler -o yaml
```
- **Sample partial output**
```yml
    spec:
      containers:
      - command:
        - ./cluster-autoscaler
        - --v=4
        - --stderrthreshold=info
        - --cloud-provider=aws
        - --skip-nodes-with-local-storage=false
        - --expander=least-waste
        - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/eksdemo1
        - --balance-similar-node-groups
        - --skip-nodes-with-system-pods=false
        image: us.gcr.io/k8s-artifacts-prod/autoscaling/cluster-autoscaler:v1.16.5
```

## Step-07: View Cluster Autoscaler logs to verify that it is monitoring your cluster load.
```
kubectl -n kube-system logs -f deployment.apps/cluster-autoscaler
```

