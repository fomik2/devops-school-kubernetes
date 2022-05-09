### Таск 3 ###  
*We published minio "outside" using nodePort. Do the same but using ingress.*  
*Publish minio via ingress so that minio by ip_minikube and nginx returning hostname (previous job) by path ip_minikube/web are available at the same time.*  
*Create deploy with emptyDir save data to mountPoint emptyDir, delete pods, check data.*  

1. Create Ingress for minio. At this stage we can open ip_minikube by http and enter minio   
`kubectl apply -f ingress-minio.yaml`   

2. Do the task "nginx returning hostname (previous job) by path ip_minikube/web". Create nginx and service:   
`kubectl apply -f nginx.yaml`  

3. Create ingress for nginx. On this stage we can open minio by ip_minikube and ip_mininkube/web return hostname to us.  
`kubectl apply -f nginx-ingress.yaml`  

4. Do the task "Create deploy with emptyDir save data to mountPoint emptyDir, delete pods and then check data. Result manifest is nginx-emptyDir.yaml in this repo   
    - add emptyDir volume to nginx.yaml
	```
	volumes:
        - name: nginx-conf
          configMap:
            name: nginx-conf
            # items:
              # - key: default.conf
                # path: default.conf
        - name: emptydir
          emptyDir: {}
	```
	- add VolumeMount to container:
	```
	volumeMounts:
          - name: nginx-conf 
            mountPath: /etc/nginx/conf.d
            readOnly: true
          - name: emptydir
            mountPath: /tmp/emptydir       
	```
	- then check mount, delete pod and check it again:
	```
	minikube ssh
	$ sudo find / -name my_test_file
	/mnt/sda1/var/lib/kubelet/pods/0738edda-085f-4863-afd4-590711c966e1/volumes/kubernetes.io~empty-dir/emptydir/my_test_file
	/var/lib/kubelet/pods/0738edda-085f-4863-afd4-590711c966e1/volumes/kubernetes.io~empty-dir/emptydir/my_test_file
	
	kubectl delete -f nginx.yaml
	sudo find / -name my_test_file find nothing
	```
