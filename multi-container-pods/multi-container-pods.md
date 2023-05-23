29. Create a Pod with three busy box containers with commands “ls; sleep 3600;”, “echo Hello World; sleep 3600;” and “echo this is the third container; sleep 3600” respectively and check the status

// first create single container pod with dry run flag  
`kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml -- bin/sh -c "sleep 3600; ls" > multi-container.yaml`
// edit the pod to following yaml and create it  
`kubectl create -f multi-container.yaml`
`kubectl get po busybox`

multi-container pod
30. Check the logs of each container that you just created

`kubectl logs busybox -c busybox1
kubectl logs busybox -c busybox2
kubectl logs busybox -c busybox3`

31. Check the previous logs of the second container busybox2 if any  
`kubectl logs busybox -c busybox2 --previous`

32. Run command ls in the third container busybox3 of the above pod  
`kubectl exec busybox -c busybox3 -- ls`

33. Show metrics of the above pod containers and puts them into the file.log and verify  
Check if the Metrics Server is already installed:  
`kubectl get deployment metrics-server -n kube-system`  
If it is not installed, proceed to the next step.  

Install the Metrics Server using the official manifests:  

#if  metrics-server is not installed  
`kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`  

Verify that the Metrics Server is running:  
`kubectl get deployment metrics-server -n kube-system`  

#now installed  

`kubectl top pod busybox --containers`  
// putting them into file  
`kubectl top pod busybox --containers > file.log`  
`cat file.log`  
See the logs  

34. Create a Pod with main container busybox and which executes this “while true; do echo ‘Hi I am from Main container’ >> /var/log/index.html; sleep 5; done” and with sidecar container with nginx image which exposes on port 80. Use emptyDir Volume and mount this volume on path /var/log for busybox and on path /usr/share/nginx/html for nginx container. Verify both containers are running.  

create an initial yaml file with this  
`kubectl run multi-cont-pod --image=busbox --restart=Never --dry-run -o yaml > multi-container.yaml `  

edit the yml as below and create it  
`kubectl create -f multi-container.yaml`  
`kubectl get po multi-cont-pod`  

multi-container.yaml  

35. Exec into both containers and verify that main.txt exist and query the main.txt from sidecar container with curl localhost  
// exec into main container  
`kubectl exec -it  multi-cont-pod -c main-container -- sh`  
`cat /var/log/main.txt`  
// exec into sidecar container  
`kubectl exec -it  multi-cont-pod -c sidecar-container -- sh`  
`cat /usr/share/nginx/html/index.html`  
// install curl and get default page  
`kubectl exec -it  multi-cont-pod -c sidecar-container -- sh`  
use following commands in bash
` apt-get update && apt-get install -y curl  
  curl localhost`  
You will see following output:  
`Hi I am from Main container  
Hi I am from Main container  
Hi I am from Main container  
Hi I am from Main container  
Hi I am from Main container  
Hi I am from Main container  
Hi I am from Main container  
Hi I am from Main container  
Hi I am from Main container`
