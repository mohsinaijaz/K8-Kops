Here are the steps to install Kubernetes cluster using Kops in AWS.

link to kops https://github.com/kubernetes/kops

User who will create the cluster must have the following IAM permissions:
1. AmazonEC2FullAccess
2. AmazonRoute53FullAccess
3. AmazonS3FullAccess
4. IAMFullAccess
5. AmazonVPCFullAccess

Kops will create the following in your AWS account, make sure to delete the cluster to avoid charges.

#

Setup aws cli to interact with aws account

$ aws configure

Install kops and kubectl interact with the cluster

$ brew update && brew install kops kubectl

$ aws s3api create-bucket --bucket <****create-kops-state-bucket***> --region us-east-1

$ aws s3api put-bucket-versioning --bucket <****same-bucket-name***>  --versioning-configuration Status=Enabled

$ export KOPS_CLUSTER_NAME=<*****nameyoucluster****.k8s.local>

$ export KOPS_STATE_STORE=s3://<****same as bucket name***>

**Feel free to remove or change anything in the create command to fit your needs.**

The below commands will create a cluster configuration with in AWS with t2 instances (Use any of the 3 command below to create)

$ kops create cluster --node-count=4 --node-size=t2.medium --cloud=aws --zones=us-east-1a --name=<*****nameyoucluster****.k8s.local> --cloud=aws --cloud-labels="Stack=Test,Application=Frontend"

$ kops create cluster --name=<*****nameyoucluster****.k8s.local> --state=s3://<****same-bucket-name***> --zones=us-east-1a --node-count=2 --networking weave --topology private --bastion="true"

$ kops create cluster --node-count=2 --node-size=t2.medium --zones=us-east-1a

**We can then spin up our cluster using:**

$ kops update cluster --name ${KOPS_CLUSTER_NAME} --yes

$ kops update cluster --name=<*****nameyoucluster****.k8s.local> --yes --state=s3://<****same-bucket-name***>

$ kops validate cluster

$ kubectl get nodes --show-lables

$ kops delete cluster --name ${KOPS_CLUSTER_NAME} --yes
