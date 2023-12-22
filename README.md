# AWS-EKS-MongoDB-ReplicaSet

## 1. Run Docker Desktop

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/155a6ddb-7518-4ba0-9229-7e7aed13d738)

Enable Kubernetes 

## 2. Create AWS EKS (Elastic Kubernetes Cluster)

```
eksctl create cluster ^
  --name dotnet8webapi-1974123 ^
  --version 1.24 ^
  --region eu-west-3 ^
  --nodegroup-name linux-nodes ^
  --node-type t2.micro ^
  --nodes 1
```

**NOTE**: If you get an error during the cluster creation due to name is not unique, delete the cluster with the following command and input a new cluster name 

```
eksctl delete cluster --region=eu-west-3 --name=dotnet8webapi-1974123
```

## 3. 

```
kubectl config get-contexts
```

## 4. 




