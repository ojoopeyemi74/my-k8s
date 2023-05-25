# In this section we are using ingress controller to load balance the load accross multiple hosts 

# the file named "deploy.yaml is the Ingress controller 
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.7.1/deploy/static/provider/aws/deploy.yaml
```

# if for example you have about 10 webpage which you would like to access on the browser, in K8s the best way to go about it is using LB, but it will be too expensive to have LB for each webpages

# Ingress solved this problem for us, using nginx-ingress controller, once we have that downloaded and installed on our cluster, it will create a LB for us on the cloud, 

- next: we will create a yaml file to route all our webpage using the service name, as the webages service will be ClusterIP, and with each definition with their host

- next: with that all done, nginx-ingress controller will help us route traffic to all the webpage using the same LB it creates earlir, which means we will only be using one LB for multiple webpages

# NOTE: we also have to create IngressClass
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-myservicea
spec:
  rules:
  - host: myservicea.foo.org
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myservicea
            port:
              number: 80
  ingressClassName: nginx

```