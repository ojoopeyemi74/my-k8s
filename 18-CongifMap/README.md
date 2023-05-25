# In this section we will be looking at our configMap works, and there are several ways of implementing configMap

# why do we use configMap?
for example we need to pass the same details to several pods on our clusters, e.g passing an ENV, passing the env to each pod one at a time is not a good practice, so we use configMap and refrence it in all of our pods

# NOTE:

# the con of CM is that for example when you change a detail in your CM, it doesnt automatically reflect inside your already runnigng pods, and one way to go about it is rebouncing your pods, by delete each pod at a time so it can respawn and pick up the new CM


# ANOTHER WAY OF UPDATING CHNAGE IN CM
  another way of updating the pods to pick the new change in CM is to create a new version of the CM

# before
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: global-database-config-v1
data:
  database.url: "https://mysql.somewhere.com:3306"
  database.password: "Password"
```

# after
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: global-database-config-v2
data:
  database.url: "https://newly-updated-mysql.somewhere.com:3306"
  database.password: "Password"
```

# then apply the kubectl apply -f on the configmap

- next: you will also need to update all your deployment yaml file to change the configMap name
```
configMapKeyRef:
            name: global-database-config-v3
```

# and then re-apply your deployment which will force all the pods to reload and now pick the new configMap update

- next: the file in the 01-example1 is the first way 


# In the Second example EnvFrom, since it is about having a configMap specifcally for enviroment, the key in the cm has to be CAPITAL letters 
example:
```
data:
  DATABASE_URL: "https://dbserver.somewhere.com:3306"
  DATABASE_PASSWORD: "Password"
```


# EXAMPLE 3
 
 In this third example, we are using volumeMount to pass a file to our pods using configMap


 - steps: we created a volumeMounts with the path we want the file to be placed inside our pods

 - step2: we add volumes, the volume indentation as to be directly under containers, and make sure the name in the same with the name in the configmap yaml

 ```

        volumeMounts:
          - name:  database-config-volume
            mountPath: /etc/any/directory/config
      volumes:
        - name: database-config-volume
          configMap:
           name: global-database-config-v3
    ```