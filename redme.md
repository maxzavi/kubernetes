# Namespace

Create namespace in cluster, use file namespace.yml:

```cmd
kubectl apply -f namespace.yml
```

Validate
```cmd
kubectl get namespace my-namespace
```

Sett the namespace by default:

```cmd
kubectl config set-context --current --namespace=my-namespace
```
