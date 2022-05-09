### Таск 4 ###  
- *Create users deploy_view and deploy_edit. Give the user deploy_view rights only to view deployments, pods. Give the user deploy_edit full rights to the objects deployments, pods.*  
- *Create namespace prod. Create users prod_admin, prod_view. Give the user prod_admin admin rights on ns prod, give the user prod_view only view rights on namespace prod.*  
- *Create a serviceAccount sa-namespace-admin. Grant full rights to namespace default. Create context, authorize using the created sa, check accesses.*  

1. Create users deploy_view and deploy_edit. Create contex for this users too.    
```
openssl genrsa -out deploy_view.key 2048
openssl req -new -key deploy_view.key -out deploy_view.csr -subj "/CN=deploy_view"
openssl x509 -req -in deploy_view.csr -CA C:\Users\vsfomin\.minikube\ca.crt -CAkey C:\Users\vsfomin\.minikube\ca.key -CAcreateserial -out deploy_view.crt -days 500
kubectl config set-credentials deploy_view --client-certificate=deploy_view.crt --client-key=deploy_view.key
kubectl config set-context deploy_view --cluster=minikube --user=deploy_view


openssl genrsa -out deploy_edit.key 2048
openssl req -new -key deploy_edit.key -out deploy_edit.csr -subj "/CN=deploy_edit"
openssl x509 -req -in deploy_edit.csr -CA C:\Users\vsfomin\.minikube\ca.crt -CAkey C:\Users\vsfomin\.minikube\ca.key -CAcreateserial -out deploy_edit.crt -days 500
kubectl config set-credentials deploy_edit --client-certificate=deploy_edit.crt --client-key=deploy_edit.key
kubectl config set-context deploy_edit --cluster=minikube --user=deploy_edit

```

2. Apply roles and bind this roles to each users     
`kubectl apply -f deploy-view.yaml`  
`kubectl apply -f deploy-edit.yaml`  

3. Test grants for each user. Try to list pods and deployment, them create pod. First for user deploy_view, after for user deploy_edit
```
kubectl config use-context deploy_view
kubectl get pods
kubectl get deployments
kubectl run nginx --image=nginx --restart=Never

```  
---
1. Create namespace prod
`kubectl create ns prod`

2. Create users prod_admin and prod_view
```
openssl genrsa -out prod_admin.key 2048
openssl req -new -key prod_admin.key -out prod_admin.csr -subj "/CN=prod_admin"
openssl x509 -req -in prod_admin.csr -CA C:\Users\vsfomin\.minikube\ca.crt -CAkey C:\Users\vsfomin\.minikube\ca.key -CAcreateserial -out prod_admin.crt -days 500
kubectl config set-credentials prod_admin --client-certificate=prod_admin.crt --client-key=prod_admin.key
kubectl config set-context prod_admin --cluster=minikube --user=prod_admin

openssl genrsa -out prod_view.key 2048
openssl req -new -key prod_view.key -out prod_view.csr -subj "/CN=prod_view"
openssl x509 -req -in prod_view.csr -CA C:\Users\vsfomin\.minikube\ca.crt -CAkey C:\Users\vsfomin\.minikube\ca.key -CAcreateserial -out prod_view.crt -days 500
kubectl config set-credentials prod_view --client-certificate=prod_view.crt --client-key=prod_view.key
kubectl config set-context prod_view --cluster=minikube --user=prod_view
```

3. Apply roles and bind this roles to each users     
`kubectl apply -f prod_admin.yaml`  
`kubectl apply -f prod_view.yaml`  

4. Test grants:
```
kubectl config use-context prod_view
kubectl get pods -- this command is forbidden 
kubectl get pods -n prod -- this command is ok
kubectl run nginx --image=nginx --restart=Never -n prod -- this command is forbidden


kubectl config use-context prod_admin
kubectl get pods -- this command is forbidden 
kubectl get pods -n prod -- this command is ok
kubectl run nginx --image=nginx --restart=Never -n prod -- this command is OK
```

---

1. Create service account, role and bind role to this account  
`kubectl apply -f serviceacc.yaml`

2. Create contex and test grants
`kubectl describe sa` -- берем имя токена  
`kubectl get secrets -n default sa-namespace-admin-token-m8dbq -o json`  -- берем сам токен и делаем decode base64
`kubectl config set-context sa-namespace-admin --cluster=minikube --user=sa-namespace-admin`  
`kubectl config use-context sa-namespace-admin`  


eyJhbGciOiJSUzI1NiIsImtpZCI6InNEYlVuMXZwY0NXMGhBWFIxR0dkM0ZLLWZBb1dSM1NjeUtuVE0xTkh4dXMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6InNhLW5hbWVzcGFjZS1hZG1pbi10b2tlbi1tOGRicSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJzYS1uYW1lc3BhY2UtYWRtaW4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJkNDFlYzUwMi1hY2Q2LTRjMTgtODdkYy1jYWM0N2NhMmU2MzYiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpzYS1uYW1lc3BhY2UtYWRtaW4ifQ.MwsGXH3qQ2rNbYx_LWhHiZNvpNFZRV_-kXR7tgSQAH01zb2kT6UvI6P8QCkMvTugp001ScHmlWGKe-o_NLvSHFBwCov0LMfbcqWn0EKG-8w2Bs0tZtA8ycj1f6rK_2ZYqeql3mHnYbUWdZN1jZz36sKNBLAuPhAes3M7MnAc8q8kPXfBZmERqjp_RgGnVXHfLn-vaV282JXJyg0Y5vjB0XmldrdBaQ8AOdF77n-LJYKw364jap5E5_AVQTvG-3gpESmIxNi2Yex4WSC2hquMiUITAEfogftaEwE167-JrcYnvQy4amcYTjLoQWti0T6mrbYOU5zAlom36dPq8VENpg