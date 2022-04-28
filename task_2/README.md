### Таск 2 ###  
>Implement Canary deployment of an application via Ingress. Traffic to canary deployment should be redirected if you add "canary:always" in the header, otherwise it should go to regular deployment.  
Set to redirect a percentage of traffic to canary deployment.  

1. Create two namespaces in our kubernetes cluster:  
`kubectl create ns echo-canary`  
`kubectl create ns echo-non-canary`  

2. Apply deployment common.yaml to namespaces created in previous step:  
`kubectl apply -f common.yaml -n echo-canary` 
`kubectl apply -f common.yaml -n echo-non-canary`  

3. Apply ingress to namespaces created in previous step:  
`kubectl apply -f ingress-non-canary.yaml -n echo-non-canary`  
`kubectl apply -f ingress-canary.yaml -n echo-canary`  

4. Let's rock!  
`curl --header "canary:always" http://myminikube.com` 
```console
echo-canary
```

`curl  http://myminikube.com`  
```console
echo-non-canary
```
