# kops_installation_aws

## Install kops CLI ##
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops


## Install kubeclt CLI ##
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl

## Create S3 bucket for KOps ##
aws s3 mb s3://kops.cerebrone.com --region us-east-1


## Adding ENV variables to Bashrc ##
vi ~/.bashrc
   export KOPS_CLUSTER_NAME=kops.cerebrone.com
   export KOPS_STATE_STORE=s3://kops.cerebrone.com

source ~/.bashrc

## Creating Cluster using KOps ##
kops create cluster --state=${KOPS_STATE_STORE} --node-count=2 --master-size=t2.medium --node-size=t2.medium --zones=us-east-1a --name=${KOPS_CLUSTER_NAME} --dns private --master-count 1 --networking flannel --image=ami-042e8287309f5df03

## Updating the Cluster ##
kops update cluster --name kops.cerebrone.com --yes --admin

## Validating Cluster ##
kops validate cluster --wait 10m


## Doing SSH into MASTER Node ##
ssh -i ~/.ssh/id_rsa ubuntu@api.kops.cerebrone.com

## Checking Available Nodes ##
kubectl get nodes


## Editing InstanceGroups to change Number of Nodes in Cluster ##

kops get instancegroup
kops edit instancegroup < node-name >

## Updating Cluster after making Changes ##

kops update cluster --yes --admin

## Installing Helm into Cluster ##

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
 
chmod 700 get_helm.sh

./get_helm.sh

helm version
