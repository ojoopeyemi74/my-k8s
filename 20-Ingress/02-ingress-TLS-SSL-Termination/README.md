# In this section we have been able to have a SSL/TLS termination 

# in this example we have used nginx and apache httpd image 

- first step: 

```
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.7.1/deploy/static/provider/aws/nlb-with-tls-termination/deploy.yaml
```

Edit the file and change the VPC CIDR in use for the Kubernetes cluster:
on the AWS console, search for your cluster VPC cidr and replace it 


```
proxy-real-ip-cidr: XXX.XXX.XXX/XX
```

Change the AWS Certificate Manager (ACM) ID as well:
on the console check for the AMC cert arn and replace it 

```
arn:aws:acm:us-west-2:XXXXXXXX:certificate/XXXXXX-XXXXXXX-XXXXXXX-XXXXXXXX
```

Deploy the manifest:


kubectl apply -f deploy.yaml