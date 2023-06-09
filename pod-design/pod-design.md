36. Get the pods with label information  
`kubectl get pods --show-labels`  

37. Create 5 nginx pods in which two of them is labeled env=prod and three of them is labeled env=dev  
`kubectl run nginx-dev1 --image=nginx --restart=Never --labels=env=dev  
kubectl run nginx-dev2 --image=nginx --restart=Never --labels=env=dev  
kubectl run nginx-dev3 --image=nginx --restart=Never --labels=env=dev  
kubectl run nginx-prod1 --image=nginx --restart=Never --labels=env=prod  
kubectl run nginx-prod2 --image=nginx --restart=Never --labels=env=prod`  

38. Verify all the pods are created with correct labels  
`kubeclt get pods --show-labels` 

39. Get the pods with label env=dev  
`kubectl get pods -l env=dev`  

40. Get the pods with label env=dev and also output the labels  
`kubectl get pods -l env=dev --show-labels`  

41. Get the pods with label env=prod  
`kubectl get pods -l env=prod`  

42. Get the pods with label env=prod and also output the labels  
`kubectl get pods -l env=prod --show-labels`  

43. Get the pods with label env  
`kubectl get pods -L env`  

44. Get the pods with labels env=dev and env=prod  
`kubectl get pods -l 'env in (dev,prod)'`  

45. Get the pods with labels env=dev and env=prod and output the labels as well  
`kubectl get pods -l 'env in (dev,prod)' --show-labels`  

46. Change the label for one of the pod to env=uat and list all the pods to verify  
`kubectl label pod/nginx-dev3 env=uat --overwrite`  
`kubectl get pods --show-labels`  

47. Remove the labels for the pods that we created now and verify all the labels are removed  
`kubectl label pod nginx-dev{1..3} env-`  
`kubectl label pod nginx-prod{1..2} env-`  
`kubectl get po --show-labels`  

48. Let’s add the label app=nginx for all the pods and verify  
`kubectl label pod nginx-dev{1..3} app=nginx`  
`kubectl label pod nginx-prod{1..2} app=nginx`  
`kubectl get po --show-labels`  

49. Get all the nodes with labels (if using minikube you would get only master node)  
`kubectl get nodes --show-labels`  

50. Label the node (minikube if you are using) nodeName=nginxnode  
`kubectl label node minikube nodeName=nginxnode`  

51. Create a Pod that will be deployed on this node with the label nodeName=nginxnode  
create pod.yaml  
`kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > pod.yaml`   
add the nodeSelector like below and create the pod  
`kubectl create -f pod.yaml`  


52. Verify the pod that it is scheduled with the node selector  
`kubectl describe po nginx | grep Node-Selectors`  

53. Verify the pod nginx that we just created has this label  
`kubectl describe po nginx | grep Labels`  

54. Annotate the pods with name=webapp  
`kubectl annotate pod nginx-dev{1..3} name=webapp`  
`kubectl annotate pod nginx-prod{1..2} name=webapp`  

55. Verify the pods that have been annotated correctly  
`kubectl describe po nginx-dev{1..3} | grep -i annotations`  
`kubectl describe po nginx-prod{1..2} | grep -i annotations`  

56. Remove the annotations on the pods and verify  
`kubectl annotate pod nginx-dev{1..3} name-`  
`kubectl annotate pod nginx-prod{1..2} name-`  
`kubectl describe po nginx-dev{1..3} | grep -i annotations`  
`kubectl describe po nginx-prod{1..2} | grep -i annotations`  

57. Remove all the pods that we created so far  
`kubectl delete po --all`  

58. Create a deployment called webapp with image nginx with 5 replicas  
create webapp.yaml
`kubectl create deploy webapp --image=nginx --dry-run -o yaml > webapp.yaml`  
change the replicas to 5 in the yaml and create it  
`kubectl create -f webapp.yaml`  

59. Get the deployment you just created with labels  
`kubectl get deploy webapp --show-labels`  

60. Output the yaml file of the deployment you just created  
`kubectl get deploy webapp -o yaml`  

61. Get the pods of this deployment  
get the label of the deployment  
`kubectl get deploy --show-labels`  
get the pods with that label  
`kubectl get pods -l app=webapp`  

62. Scale the deployment from 5 replicas to 20 replicas and verify  
`kubectl scale deploy webapp --replicas=20`  
`kubectl get po -l app=webapp`  

### Note: In Kubernetes, a deployment rollout status refers to the current state of a deployment's rollout process. It provides information about the progress and success of deploying changes to a deployment's underlying pods.  

When a deployment is updated with new configurations, such as a new image version or desired replica count, Kubernetes orchestrates a rollout process to ensure the changes are applied gradually and with minimal disruption to the running application. The rollout process includes scaling up/down replicas, updating pods, and monitoring the status of the deployment.   


63. Get the deployment rollout status  
`kubectl rollout status deploy webapp`  

64. Get the replicaset that created with this deployment  
`kubectl get rs -l app=webapp`  

65. Get the yaml of the replicaset and pods of this deployment  
`kubectl get rs -l app=webapp -o yaml`  
`kubectl get po -l app=webapp -o yaml`  

66. Delete the deployment you just created and watch all the pods are also being deleted  
`kubectl delete deploy webapp`  
`kubectl get po -l app=webapp -w`  

67. Create a deployment of webapp with image nginx:1.17.1 with container port 80 and verify the image version  
create webapp.yaml
`kubectl create deploy webapp --image=nginx:1.17.1 --dry-run -o yaml > webapp.yaml`  
add the port section and create the deployment  
`kubectl create -f webapp.yaml`  
verify  
`kubectl describe deploy webapp | grep Image`  

68. Update the deployment with the image version 1.17.4 and verify  
`kubectl set image deploy/webapp nginx=nginx:1.17.4`  
`kubectl describe deploy webapp | grep Image`  

69. Check the rollout history and make sure everything is ok after the update  
`kubectl rollout history deploy webapp`  
`kubectl get deploy webapp --show-labels`  
`kubectl get rs -l app=webapp`  
`kubectl get po -l app=webapp`  

70. Undo the deployment to the previous version 1.17.1 and verify Image has the previous version  
`kubectl rollout undo deploy webapp`   
`kubectl describe deploy webapp | grep Image`  

71. Update the deployment with the image version 1.16.1 and verify the image and also check the rollout history  
`kubectl set image deploy/webapp nginx=nginx:1.16.1`  
`kubectl describe deploy webapp | grep Image`  
`kubectl rollout history deploy webapp`  

72. Update the deployment to the Image 1.17.1 and verify everything is ok  
`kubectl rollout undo deploy webapp --to-revision=3`  
`kubectl describe deploy webapp | grep Image`  
`kubectl rollout status deploy webapp`  

73. Update the deployment with the wrong image version 1.100 and verify something is wrong with the deployment  
`kubectl set image deploy/webapp nginx=nginx:1.100`  
`kubectl rollout status deploy webapp (still pending state)`  
`kubectl get pods (ImagePullErr)`  


74. Undo the deployment with the previous version and verify everything is Ok  
`kubectl rollout undo deploy webapp`  
`kubectl rollout status deploy webapp`  
`kubectl get pods`  

75. Check the history of the specific revision of that deployment  
`kubectl rollout history deploy webapp --revision=7`  

76. Pause the rollout of the deployment  
`kubectl rollout pause deploy webapp`  

77. Update the deployment with the image version latest and check the history and verify nothing is going on  
`kubectl set image deploy/webapp nginx=nginx:latest`  
`kubectl rollout history deploy webapp (No new revision)`  

78. Resume the rollout of the deployment  
`kubectl rollout resume deploy webapp`  

79. Check the rollout history and verify it has the new version  
`kubectl rollout history deploy webapp`  
`kubectl rollout history deploy webapp --revision=9`  

80. Apply the autoscaling to this deployment with minimum 10 and maximum 20 replicas and target CPU of 85% and verify hpa is created and replicas are increased to 10 from 1

kubectl autoscale deploy webapp --min=10 --max=20 --cpu-percent=85
kubectl get hpa
kubectl get pod -l app=webapp
81. Clean the cluster by deleting deployment and hpa you just created

kubectl delete deploy webapp
kubectl delete hpa webapp
82. Create a Job with an image node which prints node version and also verifies there is a pod created for this job

kubectl create job nodeversion --image=node -- node -v
kubectl get job -w
kubectl get pod
83. Get the logs of the job just created

kubectl logs <pod name> // created from the job
84.Output the yaml file for the Job with the image busybox which echos “Hello I am from job”

kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job"
85. Copy the above YAML file to hello-job.yaml file and create the job

kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
kubectl create -f hello-job.yaml
86. Verify the job and the associated pod is created and check the logs as well

kubectl get job
kubectl get po
kubectl logs hello-job-*
87. Delete the job we just created

kubectl delete job hello-job
88. Create the same job and make it run 10 times one after one

kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
// edit the yaml file to add completions: 10
kubectl create -f hello-job.yaml

hello-job.yaml
89. Watch the job that runs 10 times one by one and verify 10 pods are created and delete those after it’s completed

kubectl get job -w
kubectl get po
kubectl delete job hello-job
90. Create the same job and make it run 10 times parallel

kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
// edit the yaml file to add parallelism: 10
kubectl create -f hello-job.yaml

hello-job.yaml
91. Watch the job that runs 10 times parallelly and verify 10 pods are created and delete those after it’s completed

kubectl get job -w
kubectl get po
kubectl delete job hello-job

### Note:  a CronJob is a type of controller that allows you to schedule and automate the execution of Jobs on a recurring basis. It is designed to run tasks based on a cron-like syntax, similar to the popular cron utility found in Unix-like operating systems. It provides capabilities such as Scheduling, Recurring jobs, parallel execution, job management, logging and monitoring, etc. 

92. Create a Cronjob with busybox image that prints date and hello from kubernetes cluster message for every minute

kubectl create cronjob date-job --image=busybox --schedule="*/1 * * * *" -- bin/sh -c "date; echo Hello from kubernetes cluster"
93. Output the YAML file of the above cronjob

kubectl get cj date-job -o yaml
94. Verify that CronJob creating a separate job and pods for every minute to run and verify the logs of the pod

kubectl get job
kubectl get po
kubectl logs date-job-<jobid>-<pod>
95. Delete the CronJob and verify all the associated jobs and pods are also deleted.

kubectl delete cj date-job
// verify pods and jobs
kubectl get po
kubectl get job

