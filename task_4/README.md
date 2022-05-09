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
`kubectl config set-context sa-namespace-admin --cluster=minikube --user=sa-namespace-admin`
`kubectl config use-context sa-namespace-admin`
