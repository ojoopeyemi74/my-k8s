# in this section we will be setting up EKS

# we have been able to lunch an EC2 instance named bootstrap, and on the Ec2 instance we have attached all the needed permission needed for our cluster for function

# we have been having issues starting PV, it has been stucked on pending on the ( EKS latest version), but it has been working find on EKS version 1.22

eksctl create cluster --name fleetman --version 1.22 --nodes-min 3


# we could check to see where on what node our pod is currently running using

kubectl get pods -o wide

