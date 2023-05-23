# Configuration (18%)


### Practice questions based on these concepts

Understand ConfigMaps
Understand SecurityContexts
Define an application’s resource requirements
Create & Consume Secrets
Understand ServiceAccounts

### Note: ConfigMap is an API object used to store and manage configuration data for applications. It provides a way to separate configuration data from application code, allowing for easier management and customization of application settings.

105. List all the configmaps in the cluster

kubectl get cm
     or
kubectl get configmap
106. Create a configmap called myconfigmap with literal value appname=myapp

kubectl create cm myconfigmap --from-literal=appname=myapp
107. Verify the configmap we just created has this data

// you will see under data
kubectl get cm -o yaml
         or
kubectl describe cm
108. delete the configmap myconfigmap we just created

kubectl delete cm myconfigmap
109. Create a file called config.txt with two values key1=value1 and key2=value2 and verify the file

cat >> config.txt << EOF
key1=value1
key2=value2
EOF
cat config.txt
110. Create a configmap named keyvalcfgmap and read data from the file config.txt and verify that configmap is created correctly

kubectl create cm keyvalcfgmap --from-file=config.txt
kubectl get cm keyvalcfgmap -o yaml
111. Create an nginx pod and load environment values from the above configmap keyvalcfgmap and exec into the pod and verify the environment variables and delete the pod

// first run this command to save the pod yml
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-pod.yml
// edit the yml to below file and create
kubectl create -f nginx-pod.yml
// verify
kubectl exec -it nginx -- env
kubectl delete po nginx

nginx-pod.yml
112. Create an env file file.env with var1=val1 and create a configmap envcfgmap from this env file and verify the configmap

echo var1=val1 > file.env
cat file.env
kubectl create cm envcfgmap --from-env-file=file.env
kubectl get cm envcfgmap -o yaml --export
113. Create an nginx pod and load environment values from the above configmap envcfgmap and exec into the pod and verify the environment variables and delete the pod

// first run this command to save the pod yml
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-pod.yml
// edit the yml to below file and create
kubectl create -f nginx-pod.yml
// verify
kubectl exec -it nginx -- env
kubectl delete po nginx

nginx-pod.yaml
114. Create a configmap called cfgvolume with values var1=val1, var2=val2 and create an nginx pod with volume nginx-volume which reads data from this configmap cfgvolume and put it on the path /etc/cfg

// first create a configmap cfgvolume
kubectl create cm cfgvolume --from-literal=var1=val1 --from-literal=var2=val2
// verify the configmap
kubectl describe cm cfgvolume
// create the config map 
kubectl create -f nginx-volume.yml
// exec into the pod
kubectl exec -it nginx -- /bin/sh
// check the path
cd /etc/cfg
ls

nginx-volume.yml
115. Create a pod called secbusybox with the image busybox which executes command sleep 3600 and makes sure any Containers in the Pod, all processes run with user ID 1000 and with group id 2000 and verify.

// create yml file with dry-run
kubectl run secbusybox --image=busybox --restart=Never --dry-run -o yaml -- /bin/sh -c "sleep 3600;" > busybox.yml
// edit the pod like below and create
kubectl create -f busybox.yml
// verify
kubectl exec -it secbusybox -- sh
id // it will show the id and group

busybox.yml
116. Create the same pod as above this time set the securityContext for the container as well and verify that the securityContext of container overrides the Pod level securityContext.

// create yml file with dry-run
kubectl run secbusybox --image=busybox --restart=Never --dry-run -o yaml -- /bin/sh -c "sleep 3600;" > busybox.yml
// edit the pod like below and create
kubectl create -f busybox.yml
// verify
kubectl exec -it secbusybox -- sh
id // you can see container securityContext overides the Pod level

busybox.yml
117. Create pod with an nginx image and configure the pod with capabilities NET_ADMIN and SYS_TIME verify the capabilities

// create the yaml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// edit as below and create pod
kubectl create -f nginx.yml
// exec and verify
kubectl exec -it nginx -- sh
cd /proc/1
cat status
// you should see these values
CapPrm: 00000000aa0435fb
CapEff: 00000000aa0435fb

nginx.yml
118. Create a Pod nginx and specify a memory request and a memory limit of 100Mi and 200Mi respectively.

// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl top pod

nginx.yml
119. Create a Pod nginx and specify a CPU request and a CPU limit of 0.5 and 1 respectively.

// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl top pod

nginx.yml
120. Create a Pod nginx and specify both CPU, memory requests and limits together and verify.

// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl top pod

nginx.yml
121. Create a Pod nginx and specify a memory request and a memory limit of 100Gi and 200Gi respectively which is too big for the nodes and verify pod fails to start because of insufficient memory

// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add the resources section and create
kubectl create -f nginx.yml
// verify
kubectl describe po nginx // you can see pending state

nginx.yml
122. Create a secret mysecret with values user=myuser and password=mypassword

kubectl create secret generic my-secret --from-literal=username=user --from-literal=password=mypassword
123. List the secrets in all namespaces

kubectl get secret --all-namespaces
124. Output the yaml of the secret created above

kubectl get secret my-secret -o yaml
125. Create an nginx pod which reads username as the environment variable

// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add env section below and create
kubectl create -f nginx.yml
//verify
kubectl exec -it nginx -- env

nginx.yml
126. Create an nginx pod which loads the secret as environment variables

// create a yml file
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx.yml
// add env section below and create
kubectl create -f nginx.yml
//verify
kubectl exec -it nginx -- env

nginx.yml
127. List all the service accounts in the default namespace

kubectl get sa
128. List all the service accounts in all namespaces

kubectl get sa --all-namespaces
129. Create a service account called admin

kubectl create sa admin
130. Output the YAML file for the service account we just created

kubectl get sa admin -o yaml
131. Create a busybox pod which executes this command sleep 3600 with the service account admin and verify

kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml -- /bin/sh -c "sleep 3600" > busybox.yml
kubectl create -f busybox.yml
// verify
kubectl describe po busybox
