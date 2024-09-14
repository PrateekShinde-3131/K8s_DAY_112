# Kubernetes DaemonSet and CronJob Example
This README provides steps to create a DaemonSet running an NGINX container on all Kubernetes nodes and a CronJob that prints "40daysofkubernetes" every 5 minutes using the busybox image.

## Prerequisites
- A running Kubernetes cluster
- kubectl installed and configured
- Basic knowledge of Kubernetes resources like DaemonSets and CronJobs

## DaemonSet Setup
A DaemonSet ensures that a specified pod runs on all (or some) nodes in a Kubernetes cluster. In this case, we will deploy an nginx container to all nodes using a DaemonSet.

### DaemonSet Manifest
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-ds
  labels: 
    env: demo
spec:
  template:
    metadata:
      name: nginx
      labels: 
        env: demo
    spec:
      containers: 
      
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
  selector:
    matchLabels:
      env: demo
```
### Steps to Apply the DaemonSet
 1. Save the above YAML as nginx-daemonset.yaml.
 2. Apply the DaemonSet using kubectl:

 ```
 kubectl apply -f nginx-daemonset.yaml
 ```
 3. Verify that the DaemonSet has been created:
 ```
 kubectl get daemonset
 ```
 4. Check the pods deployed by the DaemonSet:
 ```
 kubectl get pods -o wide
 ```

## CronJob Setup
A CronJob is used to schedule a job that runs periodically. Here, we will set up a CronJob that prints "40daysofkubernetes" every 5 minutes.

### CronJob Manifest
```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/5 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo 40daysofkubernetes
          restartPolicy: OnFailure          
```
### Steps to Apply the CronJob
 1. Save the YAML as cronjob.yaml.
 2. Apply the CronJob using kubectl:

```
kubectl apply -f cronjob.yaml
```
 3. Check the CronJob status:
```
kubectl get cronjob
```
 4. Verify job execution logs:
```
# Replace "hello-4111706356" with the job name in your system
pods=$(kubectl get pods --selector=job-name=hello-4111706356 --output=jsonpath={.items[*].metadata.name})
```

 Show the pod log:
```
kubectl logs $pods
```