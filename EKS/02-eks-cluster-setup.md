# Install EKS

Please follow the prerequisites doc before this.

## Install using Fargate

```
eksctl create cluster \
--name robot-shop-eks-cluster \
--region us-east-1 \
--zones us-east-1a,us-east-1b \
--nodegroup-name robot-shop-nodes \
--node-type t3.small \
--nodes 2 \
--managed
```

## Delete the cluster

```
eksctl delete cluster --name demo-cluster-eks-robot-shop --region us-east-1
```



