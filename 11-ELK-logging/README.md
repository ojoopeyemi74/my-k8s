# in this section we will be using the EFK ( Elasticsearch Fluentd and Kibana) to gather and visualize the logs of our clusters


# Fluend 
----------
Flentd helps gather all the logs on all our nodes, which means fluentd need to be installed on our cluster, and it uses DeamonSets( which enables it to be replicated and deployed on each of our nodes, so it can gather all the logs from each nodes).


# Elasticsearch
-------------
Once the flentd has been able to gather all the logs from our nodes, it won't be able to store the logs and also search for the logs, so we need the ElasticSearch replicas as well, we could use our cloud provider Elasticsearch if we don't want to manage one.

But in this section we have been able to get an ElasticSearch yaml and it will be deployed on our cluster,we have 2 replicas, as we want the availability of elasticsearch so we don't logs our already stored logs


# Kibana
-----------
Once we have our Fluentd and Elasticsearch up and running, we need a way to be able to visualize our logs in real time, and with the help of Kibana we can have a dashboard where we can easily see all the logs of what is happening on our cluster in real time..