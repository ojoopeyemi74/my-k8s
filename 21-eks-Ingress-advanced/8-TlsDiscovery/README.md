# IN this section we have been able to use the ingress ALB which discovered the tls certificate from our ACM without specifying the certificate arn in the yaml file

# we were able to achieve that by adding this in our ingress manifest

```
  tls:
  - hosts:
    - www.neuniktechnologygroup.com
```

# it will automatically create the url in route53 