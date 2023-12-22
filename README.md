# How to deploy .NET 6 WebAPI to AWS EKS cluster

## 0. Create a .NET 6 WebAPI with Visual Studio Community edition

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/80bfe095-2a37-4cf0-87e5-d596d339370a)

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/44cbdf18-41c3-4cb8-b063-8d65c14286a7)

Set the project name and the location

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/6ea0102a-0ecb-48c9-afdd-80173f5420f8)

Set the project features. IMPORTANT: Enable Docker suppoort for automatically create the Dockerfile

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/5b9f1d44-0daf-412f-a54c-3e956910c84c)

Add the kubernetes manifest files to the project: **deployment.yml** and **service.yml**

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/7328b368-f3b9-437b-8da0-8c0a2ca40262)

## 1. Run Docker Desktop

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/155a6ddb-7518-4ba0-9229-7e7aed13d738)

Enable Kubernetes 

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/658b6e28-aa7d-4ab7-a411-216b36ed5029)

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/ffd24cd6-be21-4086-abcd-e4cb50be949a)

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/f3569470-60e1-4abb-8268-27a5ebcad178)

## 2. Create .NET 6 Web API Docker image and upload to AWS ECR

Navigate to AWS ECR and create a public repo to store the .NET 6 WebAPI Docker image

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/a20da4ec-348e-4397-ba7b-660f4a6051e8)

Click on the created repo name and press the button **View push commands**

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/f56bca67-f0d1-42e3-ab44-823e5ec910c2)

```
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/x6y4g2f4
```

```
docker build -t dotnet6webapi .
```

```
docker tag dotnet6webapi:latest public.ecr.aws/x6y4g2f4/dotnet6webapi:latest
```

```
docker push public.ecr.aws/x6y4g2f4/dotnet6webapi:latest
```

## 3. Create AWS EKS (Elastic Kubernetes Cluster)

```
eksctl create cluster ^
--name luiscocoenriquezdotnet6webapi-cluster ^
--version 1.25 ^
--region eu-west-3 ^
--nodegroup-name linux-nodes ^
--node-type t2.micro ^
--nodes 4
```

**NOTE**: if version 1.25 is not yet working in your laptop use version 1.24.

The AWS Kubernetes cluster creation takes around or more than 1 hour

**NOTE**: If you get an error during the cluster creation due to name is not unique, delete the cluster with the following command and input a new cluster name 

```
eksctl delete cluster --region=eu-west-3 --name=luiscocoenriquezdotnet6webapi-cluster
```

## 4. Get and Set AWS Kubernetes Cluster context

```
kubectl config get-contexts
```

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/116a82ec-621d-47bc-84c0-0a2959001f64)

If you would like to delete a context 

```
kubectl config delete-context luisnewuser@luiscocoenriquezdotnet6webapi-cluster.eu-west-3.eksctl.io
```

To select a cluster where to deploy applications, run the command:

```
kubectl config use-context luisnewuser@luiscocoenriquezdotnet6webapi-cluster.eu-west-3.eksctl.io
```

## 5. Verify the kubenetes parameters

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

## 6.  Write the deployment.yml file to deploy the Kubernetes cluster

This is the source code for the **deployment.yaml** file:

**IMPORTANT**: 

In this file do not forget to set the docker image in **AWS ECR**

```
spec:
      containers:
      - name: webapidotnet8
        image: public.ecr.aws/x6y4g2f4/dotnet6webapi:latest
        ports:
        - containerPort: 8080
```

Also it is very important to set the environmental variable. 

Get the environmental variables values from the **launchSettings.json** file: 

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/049856b7-9947-49ed-a9fe-a78a16861a61)

and set it in **deployment.yml** file

```
 env:
            - name: ASPNETCORE_ENVIRONMENT
              value: Development
```

This is the **deployment.yml** source code:

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

**IMPORTANT NOTE**: take in consideration if you include in your application a connection string to a database the corresponding environmental variable also should be set (see this example)

```
 env:
            - name: ASPNETCORE_ENVIRONMENT
              value: Development
            - name: ConnectionStrings__FleetManagementDb
              valueFrom:
                  configMapKeyRef:
                    name: mongo-configmap
                    key: connection_string
```

**mongo-configmap.yml**

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongo-configmap
data:
  #connection_string: mongodb://username:password@mongo-service:27017
  #connection_string: mongodb://mongodb-service:27017
  #connection_string: mongodb://mongod-0.mongodb-service.default.svc.cluster.local:27017,mongod-1.mongodb-service.default.svc.cluster.local:27017,mongod-2.mongodb-service.default.svc.cluster.local:27017
  connection_string: mongodb://mongod-0.mongodb-service.dev.svc.cluster.local:27017,mongod-1.mongodb-service.dev.svc.cluster.local:27017,mongod-2.mongodb-service.dev.svc.cluster.local:27017
  #connection_string: mongodb://mongod-0.mongodb-service.development.svc.cluster.local:27017,mongod-1.mongodb-service.development.svc.cluster.local:27017,mongod-2.mongodb-service.development.svc.cluster.local:27017
```


## 7. This is the service.yml file to deploy the Kubernetes cluster

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

## 8. Deploy the kubernetes manifest files (deployment.yml and service.yml)

```
kubectl apply -f deployment.yml --namespace dev
```

```
kubectl apply -f service.yml --namespace dev
```

## 9. Verify the Web API endpoint

![image](https://github.com/luiscoco/AWS-EKS-MongoDB-ReplicaSet/assets/32194879/9fb5251c-87d6-416d-be98-049788ec61ef)
