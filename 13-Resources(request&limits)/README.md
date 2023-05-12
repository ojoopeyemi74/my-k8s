# why do we need to add a request to our pods ?

so that when we deploy our cluster on the cloud it will let the kube scheduler knows what exactly the memory and also the cpu we want it to alllocate to this particular pod so it can run perfectly

And when we allocate a particular memory or cpu request to a pod, it doesnt mean the pod is actually using the resource, we have only specified that we want that the pod must have it, so as a Devops engineer we need to figure out what exactly our pod needs to run so we can allocate a sensible amount of resource to our pod


# and why do we need to add limit to our resource for our pods ?

we use the limit to determine that we dont want our container or pod to go over that limit, and for any reason if the container exceed that limit we have specified that the container will be killed.

we do that so that our pod dont go rouge and star taking all the space we have in our nodes for other containers to run fine, so we clamp it and limit it to our specified memory and cpu limit