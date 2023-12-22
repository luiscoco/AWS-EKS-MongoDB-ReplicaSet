# AWS-EKS-MongoDB-ReplicaSet

## 1. Create AWS EKS (Elastic Kubernetes Cluster)

```
eksctl create cluster --name dotnet8webapi-1974123 --version 1.24 --region eu-west-3 --nodegroup-name linux-nodes --node-type t2.micro --nodes 1
```

NOTE: If you get an error due to the cluster name is not unique, delete the cluster with the following command and set a new name 

```
eksctl delete cluster --region=eu-west-3 --name=dotnet8webapi-1974123
```

## 2. 

```
kubectl config get-contexts
```

## 3. 




