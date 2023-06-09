# KOPS SETUP

- create an EC2 instance, named bootstrap

- next: ssh into the bootstrap

1: Install KOPS
------------------------
```
curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops
sudo mv kops /usr/local/bin/kops
```

2: Download kubectl ( https://pwittrock.github.io/docs/tasks/tools/install-kubectl/)
-------------------------

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl
```

3: Create a user-group with the needed permissions ( https://kops.sigs.k8s.io/getting_started/aws/ )
-----------------------------------------------

```
AmazonEC2FullAccess
AmazonRoute53FullAccess
AmazonS3FullAccess
IAMFullAccess
AmazonVPCFullAccess
AmazonSQSFullAccess
AmazonEventBridgeFullAccess
```

4: Create iamUSER and attach the above group 
---------------------
create a kops user and add the already created group to it

5: Generate access keys for the command line
----------------------------

6: update the access key and secret keys on AWS configure on the CLI
---------------------------

```
$ aws configure
```

7: check if its configured
-----------------------------
```
aws iam list-users
```

8: Create an S3 bucket
----------------------

```
aws s3api create-bucket \
    --bucket prefix-example-com-state-store \
    --region us-east-1
```

```
# Note: 
S3 requires --create-bucket-configuration LocationConstraint=<region> for regions other than us-east-1
```

Example:

```
aws s3api create-bucket --bucket prefix-example-com-state-store --create-bucket-configuration LocationConstraint=eu-west-2
```

9: We need to Version our bucket
---------------------------------

```
aws s3api put-bucket-versioning --bucket prefix-example-com-state-store --versioning-configuration Status=Enabled
```

10: We will be encrypting our bucket
-----------------------------------

```
aws s3api put-bucket-encryption --bucket prefix-example-com-state-store --server-side-encryption-configuration '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'
```

11: We need to create another s3 bucket and make it public access,
 i need to learn more on how to create a bucket with ACL enabled and making it public using the CLI
---------------------------

```
aws s3api create-bucket --bucket prefix-example-com-oidc-store --create-bucket-configuration LocationConstraint=eu-west-2
```

(making s3 puclic access and ACL enabled on d GUI)
```
 https://stackoverflow.com/questions/71080354/getting-the-bucket-does-not-allow-acls-error
```

In order for ServiceAccounts to use external permissions (aka IAM Roles for ServiceAccounts), you also need a bucket for hosting the OIDC documents.
While you can reuse the bucket above if you grant it a public ACL, we do recommend a separate bucket for these files.

The ACL must be public so that the AWS STS service can access them.



12: CREATING THE cluster
------------------------------
```
export NAME=myfirstcluster.example.com 

# EXAMMPLE: export NAME=fleetman.k8s.local
```

```
export KOPS_STATE_STORE=s3://prefix-example-com-state-store

example: export KOPS_STATE_STORE=s3://my-bucket-opeyemi

```
12:Create ssh-keygen
--------------
```
ssh-keygen -t ed25519 -C "sshkey"
```

13: Create cluster configuration
---------------------------------
aws ec2 describe-availability-zones --region eu-west-2 : once you have known the supported AZ, you can list
the ones you want in the cluster create just like the below example

```
kops create cluster \
    --name=${NAME} \
    --cloud=aws \
    --zones=eu-west-2a,eu-west-2c,eu-west-2c  \
    --discovery-store=s3://prefix-example-com-oidc-store/${NAME}/discovery
```

14: Build your cluster
-----------------------

```
kops update cluster --name ${NAME} --yes --admin
```

15: Validate your Cluster
-----------------------
```
kops validate cluster --wait 10m

kubectl -n kube-system get po
```

16: You can edit your cluster and nodes (OPTIONALLY)
--------------------------

```
export EDITOR=nano
```
To Edit cluster
```
kops edit cluster --name ${NAME}
```

To Edit the nodegroup (kops refers to the nodes as (instance-groups(ig)))
```
kops get ig --name ${NAME}
```

You can decide to increase your nodes or change the Instance-Type in a particular AZ(e.g eu-west-2a)

```
kops edit ig  nodes-eu-west-2a --name ${NAME}

kops get ig --name ${NAME}

kops update cluster --name ${NAME} --yes --admin

```


                             Delete the Cluster¶

```
kops delete cluster --name ${NAME}

kops delete cluster --name ${NAME} --yes
```