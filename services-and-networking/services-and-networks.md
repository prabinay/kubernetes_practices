# Services and Networking (13%)  
### Practice questions based on these concepts  
### Understand Services  
### Demonstrate a basic understanding of NetworkPolicies  


144. Create an nginx pod with a yaml file with label my-nginx and expose the port 80  
`kubectl run nginx --image=nginx --restart=Never --port=80 --dry-run -o yaml > nginx.yaml`  
create nginx.yaml
edit the label app: my-nginx and create the pod  
`kubectl create -f nginx.yaml`  

145. Create the service for this nginx pod with the pod selector app: my-nginx  
create nginx-svc.yaml  
create the below service  
kubectl create -f nginx-svc.yaml  

146. Find out the label of the pod and verify the service has the same label  
get the pod with labels  
`kubectl get po nginx --show-labels`  
get the service and chekc the selector column  
`kubectl get svc my-service -o wide`  

147. Delete the service and create the service with kubectl expose command and verify the label  
delete the service  
`kubectl delete svc my-service`  
create the service again  
`kubectl expose po nginx --port=80 --target-port=9376`  
verify the label  
`kubectl get svc -l app=my-nginx`   

148. Delete the service and create the service again with type NodePort  
delete the service  
`kubectl delete svc nginx`  
create service with expose command  
`kubectl expose po nginx --port=80 --type=NodePort`  

149. Create the temporary busybox pod and hit the service. Verify the service that it should return the nginx page index.html.  
get the clusterIP from this command  
`kubectl get svc nginx -o wide`  
create temporary busybox to check the nodeport  
`kubectl run busybox --image=busybox --restart=Never -it --rm -- wget -o- <Cluster IP>:80`  

150. Create a NetworkPolicy which denies all ingress traffic  
create net-policy.yaml file  
`kubectl apply -f net-policy.yaml`  

