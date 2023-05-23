# State Persistence (8%)
### Practice questions based on these concepts
### Understand PersistentVolumeClaims for Storage

### Note: Persistent Volumes (PVs) provide a way to abstract and manage storage resources in a cluster. They are used to provide durable storage for applications and decouple storage from individual pods. Persistent Volumes (PVs) provide a way to abstract and manage storage resources in a cluster. They are used to provide durable storage for applications and decouple storage from individual pods. 

96. List Persistent Volumes in the cluster

kubectl get pv
97. Create a hostPath PersistentVolume named task-pv-volume with storage 10Gi, access modes ReadWriteOnce, storageClassName manual, and volume at /mnt/data and verify

kubectl create -f task-pv-volume.yaml
kubectl get pv

task-pv-volume.yaml
98. Create a PersistentVolumeClaim of at least 3Gi storage and access mode ReadWriteOnce and verify status is Bound

kubectl create -f task-pv-claim.yaml
kubectl get pvc

task-pv-claim.yaml
99. Delete persistent volume and PersistentVolumeClaim we just created

kubectl delete pvc task-pv-claim
kubectl delete pv task-pv-volume
100. Create a Pod with an image Redis and configure a volume that lasts for the lifetime of the Pod

// emptyDir is the volume that lasts for the life of the pod
kubectl create -f redis-storage.yaml

101. Exec into the above pod and create a file named file.txt with the text ‘This is called the file’ in the path /data/redis and open another tab and exec again with the same pod and verifies file exist in the same path.

// first terminal
kubectl exec -it redis-storage /bin/sh
cd /data/redis
echo 'This is called the file' > file.txt
//open another tab
kubectl exec -it redis-storage /bin/sh
cat /data/redis/file.txt
102. Delete the above pod and create again from the same yaml file and verifies there is no file.txt in the path /data/redis

kubectl delete pod redis
kubectl create -f redis-storage.yaml
kubectl exec -it redis-storage /bin/sh
cat /data/redis/file.txt // file doesn't exist
103. Create PersistentVolume named task-pv-volume with storage 10Gi, access modes ReadWriteOnce, storageClassName manual, and volume at /mnt/data and Create a PersistentVolumeClaim of at least 3Gi storage and access mode ReadWriteOnce and verify status is Bound

kubectl create -f task-pv-volume.yaml
kubectl create -f task-pv-claim.yaml
kubectl get pv
kubectl get pvc
104. Create an nginx pod with containerPort 80 and with a PersistentVolumeClaim task-pv-claim and has a mouth path "/usr/share/nginx/html"

kubectl create -f task-pv-pod.yaml

