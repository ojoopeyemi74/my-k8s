# EKS SETUP

- create an EC2 instance, named bootstrap

- next: ssh into the bootstrap

1: Install eksctl
------------------

curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp


sudo mv /tmp/eksctl /usr/local/bin

2: Update AWS CLI to Version 2
------------------------------

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

Now log out of your shell and back in again.

3: Set up a Group
-----------------

Set up a group with the Permissions of:

( you can find all the needed policy     -          https://eksctl.io/usage/minimum-iam-policies/)

AmazonEC2FullAccess
IamLimitedAccess
EksAllAccess
AWSCloudFormationFullAccess

- create an IAM limited access policy

IamLimitedAccess

```

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iam:CreateInstanceProfile",
                "iam:DeleteInstanceProfile",
                "iam:GetInstanceProfile",
                "iam:RemoveRoleFromInstanceProfile",
                "iam:GetRole",
                "iam:CreateRole",
                "iam:DeleteRole",
                "iam:AttachRolePolicy",
                "iam:PutRolePolicy",
                "iam:ListInstanceProfiles",
                "iam:AddRoleToInstanceProfile",
                "iam:ListInstanceProfilesForRole",
                "iam:PassRole",
                "iam:DetachRolePolicy",
                "iam:DeleteRolePolicy",
                "iam:GetRolePolicy",
                "iam:GetOpenIDConnectProvider",
                "iam:CreateOpenIDConnectProvider",
                "iam:DeleteOpenIDConnectProvider",
                "iam:TagOpenIDConnectProvider",
                "iam:ListAttachedRolePolicies",
                "iam:TagRole",
                "iam:GetPolicy",
                "iam:CreatePolicy",
                "iam:DeletePolicy",
                "iam:ListPolicyVersions"
            ],
            "Resource": [
                "arn:aws:iam::772745136297:instance-profile/eksctl-*",
                "arn:aws:iam::772745136297:role/eksctl-*",
                "arn:aws:iam::772745136297:policy/eksctl-*",
                "arn:aws:iam::772745136297:oidc-provider/*",
                "arn:aws:iam::772745136297:role/aws-service-role/eks-nodegroup.amazonaws.com/AWSServiceRoleForAmazonEKSNodegroup",
                "arn:aws:iam::772745136297:role/eksctl-managed-*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:GetRole"
            ],
            "Resource": [
                "arn:aws:iam::772745136297:role/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:CreateServiceLinkedRole"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:AWSServiceName": [
                        "eks.amazonaws.com",
                        "eks-nodegroup.amazonaws.com",
                        "eks-fargate.amazonaws.com"
                    ]
                }
            }
        }
    ]
}

```

You also need to create an inline policy for EksAllAccess, using the following:
--------------------------------------------------------------

EksAllAccess
```

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "eks:*",
            "Resource": "*"
        },
        {
            "Action": [
                "ssm:GetParameter",
                "ssm:GetParameters"
            ],
            "Resource": [
                "arn:aws:ssm:*:772745136297:parameter/aws/*",
                "arn:aws:ssm:*::parameter/aws/*"
            ],
            "Effect": "Allow"
        },
        {
             "Action": [
               "kms:CreateGrant",
               "kms:DescribeKey"
             ],
             "Resource": "*",
             "Effect": "Allow"
        },
        {
             "Action": [
               "logs:PutRetentionPolicy"
             ],
             "Resource": "*",
             "Effect": "Allow"
        }        
    ]
}


```

4: Create a user and add it to the group
--------------------------

Use the console to add a user to your new group - also create generate user credentials

- next: and then use "aws configure" to input the credentials

check your configureation
----------------------
aws iam list-user


5: Install kubectl
------------------

Warning: check the current default kubernetes version supplied with EKS and install a matching version of kubectl

export RELEASE=<enter default eks version number here. Eg 1.17.0>
curl -LO https://storage.googleapis.com/kubernetes-release/release/v$RELEASE/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl

Check the version with "kubectl version"


6: Start your cluster!
----------------------

eksctl create cluster --name fleetman --nodes-min=3  ( pvc has been stucked on pending on this version (persistentVolumeClaim) )

eksctl create cluster --name fleetman --version 1.22 --nodes-min 3 ( pvc as been fine to create on this version )

eksctl create cluster --name fleetman-new --version 1.22  --nodes-min=3 --node-type=t3.medium ( specifying the type of EC2 instance if you want, the default is m5.large if you didnt specify)


Delete cluster
------------------
eksctl delete cluster <cluster-name>

e.g : eksctl delete cluster fleetman-new