# AWS-EKS-MongoDB-ReplicaSet

## 1. Run Docker Desktop

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/155a6ddb-7518-4ba0-9229-7e7aed13d738)

Enable Kubernetes 

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/658b6e28-aa7d-4ab7-a411-216b36ed5029)

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/ffd24cd6-be21-4086-abcd-e4cb50be949a)

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/f3569470-60e1-4abb-8268-27a5ebcad178)

## 2. Create AWS EKS (Elastic Kubernetes Cluster)

```
eksctl create cluster ^
--name luiscocoenriquezdotnet6webapi-cluster ^
--version 1.25 ^
--region eu-west-3 ^
--nodegroup-name linux-nodes ^
--node-type t2.micro ^
--nodes 4
```

**NOTE**: If you get an error during the cluster creation due to name is not unique, delete the cluster with the following command and input a new cluster name 

```
eksctl delete cluster --region=eu-west-3 --name=dotnet8webapi-1974123
```

## 3. 

```
kubectl config get-contexts
```

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/dde4e475-0395-45e6-b781-252feb78911f)

If you would like to delete a context 

```
kubectl config delete-context contextname
```

To select a cluster where to deploy applica􀆟ons, run the command:

```
kubectl config use-context luisnewuser@dotnet8webapi-1974123.eu-west-3.eksctl.io
```


## 4. 

```
kubectl create namespace dev
```

```
kubectl get nodes
```

```
kubectl get ns
```

```
kubectl get all
```

```
kubectl get pods
```

```
kubectl get all --namespace dev
```

```
kubectl get nodes --namespace dev
```

```
kubectl get ns --namespace dev
```

```
kubectl get pods --namespace dev
```

## 5. 

```
kubectl get pods --namespace dev
```

```
kubectl get all --namespace dev
```

## 6. 

This is the source code for the **deployment.yaml** file:

**deployment.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapidotnet8-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webapidotnet8
  template:
    metadata:
      labels:
        app: webapidotnet8
    spec:
      containers:
      - name: webapidotnet8
        image: public.ecr.aws/x6y4g2f4/dotnet8webapi:latest
        ports:
        - containerPort: 8080
```

## 7. 

***service.yml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapidotnet8-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: webapidotnet8
```


## 8. 

```
kubectl apply -f deployment.yml --namespace dev
```

```
kubectl apply -f service.yml --namespace dev
```
