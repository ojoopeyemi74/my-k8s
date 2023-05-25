# In this section we are learning about Liveness and Readiness Probe

# READINESS PROBE
we have been able to define Readiness probe in our workload yaml in the api-gateway
```
 spec:
      containers:
      - name: api-gateway
        image: richardchesterwood/k8s-fleetman-api-gateway:performance
        readinessProbe:
          httpGet:
            path: /
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 3
```

# we use the readiness probe in a case where we want our pod to scale up, most times when new pods springs up, kubernetes counts them as running but it takes sometimes few secs or min for the pods to be in service to receieve traffic, and in this case k8s will be sending traffic to the pod, which means there would be down time until it is fully ready.

# so we define readiness probe which uses the health path / , which means the kublet won't send traffic to those newly created pods until they are deemed ready.

# for example in a case where we have HPA that will scale up our pods


# LIVENESS PROBE
Many applications running for long periods of time eventually transition to broken states, and cannot recover except by being restarted. Kubernetes provides liveness probes to detect and remedy such situations.

```
livenessProbe:
            exec:
              command: 
                - /bin/sh
                - -c 
                - nc -z localhost 8095
            initialDelaySeconds: 60
            periodSeconds: 10

```

              