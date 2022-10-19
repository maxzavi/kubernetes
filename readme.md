# Kubernetes


Use kubernetes cluster: cloud, onpremise, minikube, ect, to test you can use https://killercoda.com/

# Namespace

Create namespace in cluster, use manifest *namespace.yml*:

```cmd
kubectl apply -f 01-namespace.yml
```

Validate
```cmd
kubectl get namespace my-namespace
```

Set the namespace by default:

```cmd
kubectl config set-context --current --namespace=my-namespace
```

Review:
```cmd
kubectl config view | grep namespace
```

# ConfigMap & Secrets

In this case, using Api Demo with node https://github.com/maxzavi/nodeapidemo

This api uses the following environment variables:

```properties
DB_HOST="MYHOSTQA"
DB_PORT="1000"
DB_USERNAME="myuser"
DB_PASSWORD="*******"
```
So a configMap and a secret must be created, file config.yml

```cmd
kubectl apply -f 02-config.yml
```
The api image is in a private registry, access to it must be configured, as a secret:

```cmd
kubectl create secret docker-registry cr-mzavaletav --docker-server='user.mycr.io' --docker-username='mzavaletav' --docker-password='*******'
```
Find configMap key or value:

```cmd
kubectl get cm -o yaml -A | grep test
```

**For dockerhub , the --docker-server argument is not required**

**The docker-registry argument must be the same as the one used in the deployment manifest in the imagePullSecrets label.**

# Deploy

Use the service.yml manifest that contains deployment and service

```cmd
kubectl apply -f 03-service.yml
```


To test, get the port used with *kubectl get svc apinodedemo-nodeport*
```cmd
kubectl get svc apinodedemo-nodeport

NAME                   TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
apinodedemo-nodeport   NodePort   10.97.67.77   <none>        3000:31141/TCP   3m14s
```

In this case, port is 31141, being nodeport you can use the ip of any node of the cluster, in this case it is localhost

```cmd
curl localhost:31141

{"hostname":"apinodedemo-6fd97f4f7c-mz2wb","os":{"platform":"linux","release":"5.10.124-linuxkit"},"port":3000,"database":{"url":"myhostk8s","port":"1453","username":"myusername","password":"*************"}}
```

## Sidecar

Add sidecar pattern, file **04-sidecar.yml**
```cmd
kubectl apply -f 04-sidecar.yml
```


## Liveness & Readiness
Reference liveness and readiness https://medium.com/@pbaezab/liveness-and-readiness-en-1-minuto-a14bae50a8ef
Use springboot app, with initial data (RestRepository) pending repo...
For test, delete beers/1 with DELETE method

```yml
      containers:
      - image: mzavaletav/api-sb-demo:2
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /beers/1
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 10
          periodSeconds: 2
          successThreshold: 1
          timeoutSeconds: 2
```
          
