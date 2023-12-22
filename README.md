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

## 3. Get and Set AWS Kubernetes Cluster context

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

## 4. Verify the kubenetes parameters

We create a new namespace "dev"

```
kubectl create namespace dev
```

We verify the nodes

```
kubectl get nodes
```

We verify the namespaces

```
kubectl get ns
```

We verify the services, nodes, namespaces and pods

```
kubectl get all
```

We verify the pods

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

## 5.  This is the deployment.yml file to deploy the Kubernetes cluster

This is the source code for the **deployment.yaml** file:

**deployment.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapidotnet6-deployment
  labels:
    app: webapidotnet6
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapidotnet6
  template:
    metadata:
      labels:
        app: webapidotnet6
    spec:
      containers:
        - name: webapidotnet6
          image: public.ecr.aws/x6y4g2f4/dotnet6webapi:latest
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 80
          env:
            - name: ASPNETCORE_ENVIRONMENT
              value: Development
          resources:
            requests:
              memory: "64Mi"
              cpu: "250m"
            limits:
              memory: "128Mi"
              cpu: "500m"
```

## 6. This is the service.yml file to deploy the Kubernetes cluster

***service.yml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapidotnet6-service
spec:
  type: LoadBalancer
  selector:
    app: webapidotnet6
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

## 7. Deploy the kubernetes manifest files (deployment.yml and service.yml)

```
kubectl apply -f deployment.yml --namespace dev
```

```
kubectl apply -f service.yml --namespace dev
```

## 8. Verify the Web API endpoint

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/9fb5251c-87d6-416d-be98-049788ec61ef)

