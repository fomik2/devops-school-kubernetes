### Таск 1 ###  
- *Create a deployment nginx. Set up two replicas. Remove one of the pods, see what happens.*  

1. Create nginx deployment.    
`kubectl apply -f nginx.yaml`

2. Check if pod created or not. Try to delete on of them and check what happened
```
c:\manifests\education\task_1>kubectl get po
NAME                               READY   STATUS    RESTARTS       AGE
minio-94fd47554-49kb7              1/1     Running   2 (3h8m ago)   10d
minio-state-0                      1/1     Running   2 (3h8m ago)   10d
nginx-74d589986c-794ds             1/1     Running   0              15s
nginx-74d589986c-kwfwb             1/1     Running   0              15s
nginx-74d589986c-s84d6             1/1     Running   0              15s
nginx-deployment-9456bbbf9-48ls7   1/1     Running   0              152m
nginx-deployment-9456bbbf9-txkgb   1/1     Running   0              152m

c:\manifests\education\task_1>kubectl delete po nginx-74d589986c-794ds
pod "nginx-74d589986c-794ds" deleted

c:\manifests\education\task_1>kubectl get po
NAME                               READY   STATUS              RESTARTS       AGE
minio-94fd47554-49kb7              1/1     Running             2 (3h8m ago)   10d
minio-state-0                      1/1     Running             2 (3h8m ago)   10d
nginx-74d589986c-kwfwb             1/1     Running             0              38s
nginx-74d589986c-m4ptj             0/1     ContainerCreating   0              3s
nginx-74d589986c-s84d6             1/1     Running             0              38s
nginx-deployment-9456bbbf9-48ls7   1/1     Running             0              152m
nginx-deployment-9456bbbf9-txkgb   1/1     Running             0              152m
```
