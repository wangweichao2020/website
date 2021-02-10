---
title: "Long Page Title"
linkTitle: "Short Nav Title"
weight: 100
description: >-
     Page description for heading and indexes.
---

## Heading

Edit this template to create your new page.

* Give it a good name, ending in `.md` - e.g. `getting-started.md`
* Edit the "front matter" section at the top of the page (weight controls how its ordered amongst other pages in the same directory; lowest number first).
* Add a good commit message at the bottom of the page (<80 characters; use the extended description field for more detail).
* Create a new branch so you can preview your new file and request a review via Pull Request.
## Task 1

Use `k8s` context: `kubectl config use-context k8s`.

You have been tasked with a proof of concept for running an application called `Sample app` in Kubernetes. You have deployed the app as `sample-app` deployment in `sample-ns` namespace. You have done it with Kubernetes CLI, and there is no backup for your work. In order to persist the configuration, export `sample-app` deployment and the associated config map as yaml files. Save the deployment configuration to `/var/answers/deployment.yaml` and the config map to `/var/answers/configmap.yaml`.

### Solution

Start with the information provided, dump the deployment yaml:

```shell
kubectl -n sample-ns get deployment/sample-app -o yaml > /var/answers/deployment.yaml
```

Shell

Copy

Let's check what is included in the yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"labels":{"app":"sample-app"},"name":"sample-app","namespace":"sample-ns"},"spec":{"progressDeadlineSeconds":600,"replicas":1,"revisionHistoryLimit":10,"selector":{"matchLabels":{"app":"sample-app"}},"strategy":{"type":"Recreate"},"template":{"metadata":{"labels":{"app":"sample-app"}},"spec":{"containers":[{"envFrom":[{"configMapRef":{"name":"sample-app-configmap"}}],"image":"nginx","imagePullPolicy":"Always","name":"nginx","ports":[{"containerPort":80}],"resources":{},"terminationMessagePath":"/dev/termination-log","terminationMessagePolicy":"File"}],"dnsPolicy":"ClusterFirst","restartPolicy":"Always","schedulerName":"default-scheduler","securityContext":{},"terminationGracePeriodSeconds":30}}}}
  creationTimestamp: "2020-11-08T16:00:22Z"
  generation: 2
  labels:
    app: sample-app
  name: sample-app
  namespace: sample-ns
  resourceVersion: "198395"
  selfLink: /apis/apps/v1/namespaces/sample-ns/deployments/sample-app
  uid: 9cc27695-40a1-4eae-ae26-789236f3a5e9
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: sample-app
  strategy:
    type: Recreate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: sample-app
    spec:
      containers:
      - envFrom:
        - configMapRef:
            name: sample-app-configmap
        image: nginx
        imagePullPolicy: Always
        name: nginx
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  conditions:
  - lastTransitionTime: "2020-11-08T16:00:22Z"
    lastUpdateTime: "2020-11-08T16:00:22Z"
    message: Deployment does not have minimum availability.
    reason: MinimumReplicasUnavailable
    status: "False"
    type: Available
  - lastTransitionTime: "2020-11-08T17:39:00Z"
    lastUpdateTime: "2020-11-08T17:39:00Z"
    message: ReplicaSet "sample-app-fd4bf55c4" has timed out progressing.
    reason: ProgressDeadlineExceeded
    status: "False"
    type: Progressing
  observedGeneration: 2
  replicas: 1
  unavailableReplicas: 1
  updatedReplicas: 1
```

YAML

Copy

Take a look at `envFrom` section. Name of the config map is mentioned there: `sample-app-configmap`. Let's save it as a yaml file:

```shell
kubectl -n sample-ns get configmap sample-app-configmap -o yaml > /var/answers/configmap.yaml
```

Shell

Copy

------

## Task 2

Use `k8s` context: `kubectl config use-context k8s`.

You have been asked to add a couple of environment variables to `Pizza app` in `fast-food-ns` namespace. Add `CONFIG_VALUE=some_value` and `SERVICE_USER_PASS=admin1` to all `Pizza app` pods. The application prints these two variables to logs. The configuration must survive an application redeployment.

Check the application logs to validate the result. Create a command that displays logs from the last 15 seconds from all `Pizza app` pods. You have to use a single command. The source pod and timestamps must be present in the logs.

Please save the command to `/var/answers/command.txt`.

### Solution

Start with displaying the deployment information:

```shell
kubectl -n fast-food-ns describe deployment/pizza-app
```

Shell

Copy

Output:

```shell
Name:                   pizza-app
Namespace:              fast-food-ns
CreationTimestamp:      Tue, 03 Nov 2020 20:52:41 +0100
Labels:                 app=pizza-app
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=pizza-app
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=pizza-app
  Containers:
   busybox:
    Image:      busybox
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
    Args:
      while true; do echo -en '\n'; printf CONFIG_VALUE=$CONFIG_VALUE'\n'; printf SERVICE_USER_PASS=$SERVICE_USER_PASS'\n'; sleep 5; done;
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   pizza-app-7dc88449cc (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled up replica set pizza-app-7dc88449cc to 3
```

Shell

Copy

There is a section called `Environment` and this is where you need to put the additional configuration. The easiest way to achieve it is by running the following command:

```shell
kubectl -n fast-food-ns set env deployment/pizza-app CONFIG_VALUE=some_value
```

Shell

Copy

Another option is to edit the deployment:

```shell
kubectl -n fast-food-ns edit deployment/pizza-app
```

Shell

Copy

Find `env` section:

```yaml
...
- name: CONFIG_VALUE
  value: some_value
image: busybox
...
```

YAML

Copy

then add the new environment variables:

```yaml
- name: CONFIG_VALUE
  value: some_value
- name: SERVICE_USER_PASS
  value: admin1
image: busybox
```

YAML

Copy

> Take care of indentation! Use spaces over tabs!

Get info about the current deployment once again:

```shell
kubectl -n fast-food-ns  describe deployment/pizza-app
```

Shell

Copy

Output:

```shell
...
Environment:
  CONFIG_VALUE:       some_value
  SERVICE_USER_PASS:  admin1
Mounts:               <none>
...
```

Shell

Copy

As you can see, `Environment` section has been updated.

Let's proceed with the validation. Start by printing logs.

```shell
kubectl -n fast-food-ns logs deployment/pizza-app
```

Shell

Copy

This command shows output from a single pod:

```shell
Found 3 pods, using pod/pizza-app-78c75b5b8c-7zkf8

CONFIG_VALUE=some_value
SERVICE_USER_PASS=admin1
...
```

Shell

Copy

Not that bad, but we need output from all three pods. Let's try to print app logs by using a label. Also, less turn on a couple of flags:

```shell
kubectl -n fast-food-ns logs -l app=pizza-app --since=15s --prefix=true --timestamps=true
```

Shell

Copy

Sample output:

```shell
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:43.572058525Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:43.572119097Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:48.574029464Z
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:48.574056314Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:48.574061201Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:53.57534036Z
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:53.57537268Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-2tjgl/busybox] 2020-11-03T20:34:53.57537783Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:44.92228926Z
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:44.922318549Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:44.922324117Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:49.923490501Z
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:49.92352145Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:49.923527621Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:54.924726554Z
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:54.924754595Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-7zkf8/busybox] 2020-11-03T20:34:54.924759904Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:41.362211173Z
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:41.362264347Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:41.362270092Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:46.363222233Z
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:46.363259665Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:46.363267251Z SERVICE_USER_PASS=admin1
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:51.364505411Z
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:51.364531864Z CONFIG_VALUE=some_value
[pod/pizza-app-78c75b5b8c-p5kw4/busybox] 2020-11-03T20:34:51.364536735Z SERVICE_USER_PASS=admin1
```

Shell

Copy

It has worked as expected! Save the command to the desired location:

```shell
echo 'kubectl -n fast-food-ns logs -l app=pizza-app --since=15s --prefix=true --timestamps=true' > /var/answers/command.txt
```

Shell

Copy

------

## Task 3

Use `k8s` context: `kubectl config use-context k8s`.

You have been asked to externalize configuration for `money-app` deployment in `bank-ns` namespace. You have to move all environment variables from the deployment to a config map.
Add `_from_configmap` suffix to the value of each variable. The config map should be named `money-config-map`. Please store its yaml to `/var/answers/config_map.yaml`. Note, the application logs environment variables so you can use logs for troubleshooting and verification.

### Solution

Let's start with checking what `money-app` deployment looks like:

```shell
kubectl -n bank-ns describe money-app
```

Shell

Copy

As you can see, there are two environment variables defined:

```shell
Environment:
  CONFIG_VALUE_ONE:  some_value_one
  CONFIG_VALUE_TWO:  some_value_two
```

Shell

Copy

Check if they are printed in logs:

```shell
kubectl -n bank-ns logs deployment/money-app -f
```

Shell

Copy

Output:

```shell
Money app
CONFIG_VALUE_ONE=some_value_one
CONFIG_VALUE_TWO=some_value_two
```

Shell

Copy

All is good so far. Now create config map `money-config-map` and add `_from_configmap` suffix to each value:

```shell
kubectl -n bank-ns create configmap money-config-map --from-literal CONFIG_VALUE_ONE=some_value_one_from_configmap --from-literal CONFIG_VALUE_TWO=some_value_two_from_configmap
```

Shell

Copy

Now we have to plug in the newly created config map to `DeploymentConfig`. In order to do that, we have to edit `money-app` deployment:

```shell
kubectl -n bank-ns edit deployment/money-app
```

Shell

Copy

Remove `env` section:

```yaml
env:
  - name: CONFIG_VALUE_ONE
    value: some_value_one
  - name: CONFIG_VALUE_TWO
    value: some_value_two
```

YAML

Copy

Then configure the deployment to source env from `money-config-map` by adding the following section to the deployment; replacing `env` section:

```shell
envFrom:
  - configMapRef:
      name: money-config-map
```

Shell

Copy

Let's validate the application logs:

```shell
kubectl -n bank-ns logs deployment/money-app -f
```

Shell

Copy

Output:

```shell
Money app
CONFIG_VALUE_ONE=some_value_one_from_configmap
CONFIG_VALUE_TWO=some_value_two_from_configmap
```

Shell

Copy

Save `money-config-map` to `/var/answers/config_map.yaml`:

```shell
kubectl -n bank-ns get configmap/money-config-map -o yaml > /var/answers/config_map.yaml
```

Shell

Copy

It should contain output similar to:

```yaml
apiVersion: v1
data:
  CONFIG_VALUE_ONE: some_value_one_from_configmap
  CONFIG_VALUE_TWO: some_value_two_from_configmap
kind: ConfigMap
metadata:
  creationTimestamp: "2020-11-05T07:24:11Z"
  name: money-config-map
  namespace: bank-ns
  resourceVersion: "104145"
  selfLink: /api/v1/namespaces/bank-ns/configmaps/money-config-map
  uid: 53137175-81db-4dd0-9ef0-7309e4e61531
```

YAML

Copy

------

## Task 4

Use `k8s` context: `kubectl config use-context k8s`.

You work for TopSecret company. Security is the key tenet. You are building an application that needs a confidential token in order to connect to another system.

Enhance `top-app` deployment in `secret-ns` namespace to include a value for `API_TOKEN` environment variable sourced from `top-app-secret` secret. The value of the token should be set to `admin1`. For testing purposes the value of the environment variable will be printed in application logs.

### Solution

Let's check logs for `top-app` deployment:

```shell
kubectl -n secret-ns logs deployment/top-app
```

Shell

Copy

Output:

```shell
***Top app***
API_TOKEN=
```

Shell

Copy

It should change as soon as `API_TOKEN` environment variable is set.

Create `top-app-secret`:

```shell
kubectl -n secret-ns create secret generic top-app-secret --from-literal=API_TOKEN=admin1
```

Shell

Copy

Now lets insert our newly created secret into the deployment. Edit resource:

```shell
kubectl -n secret-ns edit deployment/top-app
```

Shell

Copy

and set:

```yaml
env:
  - name: API_TOKEN
    valueFrom:
      secretKeyRef:
        name: top-app-secret
        key: API_TOKEN
```

YAML

Copy

Validate application logs:

```shell
kubectl -n secret-ns logs deployment/top-app
```

Shell

Copy

Desired output:

```shell
***Top app***
API_TOKEN=admin1
```

Shell

Copy

------

## Task 5

Use `k8s` context: `kubectl config use-context k8s`.

You are a team leader of a team that is moving a huge, legacy app into the cloud. You have asked one of your developers to create a deployment for your application and run it in Kubernetes. They have run into an issue. The application is crashing upon startup. They have asked you to help.

Your need to look into the problem and make appropriate changes to the deployment. Use `big-ns` namespace.

### Solution

Let's start with checking what's going on with the deployment:

```shell
kubectl -n big-ns describe deployment/huge-app
```

Shell

Copy

Output:

```shell
kubectl -n big-ns describe deployment/huge-app
Name:               huge-app
Namespace:          big-ns
CreationTimestamp:  Fri, 06 Nov 2020 21:50:00 +0100
Labels:             app=huge-app
Annotations:        deployment.kubernetes.io/revision: 1
Selector:           app=huge-app
Replicas:           1 desired | 1 updated | 1 total | 0 available | 1 unavailable
StrategyType:       Recreate
MinReadySeconds:    0
Pod Template:
  Labels:  app=huge-app
  Containers:
   huge-app:
    Image:      polinux/stress
    Port:       <none>
    Host Port:  <none>
    Command:
      stress
    Args:
      --vm
      1
      --vm-bytes
      10M
      --vm-hang
      1
    Limits:
      memory:  32Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   huge-app-7d76b86b46 (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  23h    deployment-controller  Scaled up replica set huge-app-645ff8796d to 1
  Normal  ScalingReplicaSet  23h    deployment-controller  Scaled down replica set huge-app-645ff8796d to 0
  Normal  ScalingReplicaSet  23h    deployment-controller  Scaled up replica set huge-app-7dbccc976c to 1
  Normal  ScalingReplicaSet  8m58s  deployment-controller  Scaled down replica set huge-app-7dbccc976c to 0
  Normal  ScalingReplicaSet  8m50s  deployment-controller  Scaled up replica set huge-app-7dd8f989d to 1
  Normal  ScalingReplicaSet  7m15s  deployment-controller  Scaled down replica set huge-app-7dd8f989d to 0
  Normal  ScalingReplicaSet  6m43s  deployment-controller  Scaled up replica set huge-app-7d76b86b46 to 1
```

Shell

Copy

As you can see, the deployment is not working - no pods are available:

```shell
Replicas:           1 desired | 1 updated | 1 total | 0 available | 1 unavailable
```

Shell

Copy

Let's take a look into the pods:

```shell
kubectl -n big-ns get pods
```

Shell

Copy

Output:

```shell
NAME                        READY   STATUS             RESTARTS   AGE
huge-app-7d76b86b46-fls94   0/1     CrashLoopBackOff   8          19m
```

Shell

Copy

The pod has `CrashLoopBackOff` status. Take a look at its details:

```shell
kubectl -n big-ns describe pod huge-app-7d76b86b46-fls94
```

Shell

Copy

Output:

```shell
Name:         huge-app-7d76b86b46-fls94
Namespace:    big-ns
Priority:     0
Node:         minikube/192.168.99.100
Start Time:   Sat, 07 Nov 2020 21:40:23 +0100
Labels:       app=huge-app
              pod-template-hash=7d76b86b46
Annotations:  <none>
Status:       Running
IP:           172.17.0.8
IPs:
  IP:           172.17.0.8
Controlled By:  ReplicaSet/huge-app-7d76b86b46
Containers:
  huge-app:
    Container ID:  docker://e65bdc1c17ae9ec860a6b3c88c1c09a9f737f96369f5e5e3177865a2e9652e95
    Image:         polinux/stress
    Image ID:      docker-pullable://polinux/stress@sha256:b6144f84f9c15dac80deb48d3a646b55c7043ab1d83ea0a697c09097aaad21aa
    Port:          <none>
    Host Port:     <none>
    Command:
      stress
    Args:
      --vm
      1
      --vm-bytes
      10M
      --vm-hang
      1
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    1
      Started:      Sat, 07 Nov 2020 21:56:47 +0100
      Finished:     Sat, 07 Nov 2020 21:56:47 +0100
    Ready:          False
    Restart Count:  8
    Limits:
      memory:  32Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-dnppk (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-dnppk:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-dnppk
    Optional:    false
QoS Class:       Burstable
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  20m                 default-scheduler  Successfully assigned default/huge-app-7d76b86b46-fls94 to minikube
  Normal   Pulled     19m (x4 over 20m)   kubelet, minikube  Successfully pulled image "polinux/stress"
  Normal   Created    19m (x4 over 20m)   kubelet, minikube  Created container huge-app
  Normal   Started    19m (x4 over 20m)   kubelet, minikube  Started container huge-app
  Normal   Pulling    19m (x5 over 20m)   kubelet, minikube  Pulling image "polinux/stress"
  Warning  BackOff    39s (x94 over 20m)  kubelet, minikube  Back-off restarting failed container
```

Shell

Copy

There is plenty of information here. Take a look at `Last State` and `Reason`. `OOMKilled` means that the pod is asking for more memory than has been allocated. Check the resource limits: `32Mi`. You need to give it more. Change it to a large number to make sure it deploys successfully.

Edit the deployment resource:

```shell
kubectl -n big-ns edit deployment/huge-app`
```

Shell

Copy

Change:

```yaml
        resources:
          limits:
            memory: 32Mi
```

YAML

Copy

To:

```yaml
        resources:
          limits:
            memory: 256Mi
```

YAML

Copy

For validation, check if pods in the deployment are available:

```shell
kubectl -n big-ns describe deployment/huge-app
```

Shell

Copy

Output:

```shell
Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
```

Shell

Copy

The deployment has started successfully.

------

## Task 6

Use `dk8s` context: `kubectl config use-context dk8s`.

You are working on a small application called `simple-app`. The application runs on Kubernetes. You have been asked to improve its resiliency. You need to add a liveness probe to the deployment. Add a liveness probe to application in `simple-app` deployment in `xyz-ns` namespace. It should perform an HTTP get check for context path `/` on port `80`.

### Solution

Start by editing `simple-app` deployment:

```shell
kubectl -n xyz-ns edit deployment simple-app
```

Shell

Copy

Add the following section to the container:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
```

YAML

Copy

Validate if the deployment works ok with the liveness probe:

```shell
kubectl -n xyz-ns describe deployment simple-app
```

Shell

Copy

Output:

```shell
Name:                   simple-app
Namespace:              xyz-ns
CreationTimestamp:      Sat, 07 Nov 2020 22:43:41 +0100
Labels:                 app=simple-app
Annotations:            deployment.kubernetes.io/revision: 5
Selector:               app=simple-app
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=simple-app
  Containers:
   nginx:
    Image:        nginx:1.14.2
    Port:         80/TCP
    Host Port:    0/TCP
    Liveness:     http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
```

Shell

Copy

Take a look at `Replicas` and `Liveness` section. The check is now passing.

------

## Task 7

Use `dk8s` context: `kubectl config use-context dk8s`.

You need to prepare daily reports about your commits in a git repository. You have created a script to do the job. You have decided to run it on Kubernetes.

Your job is to create a cron job named `reporter-job` in `personal-ns` namespace that will be executed daily at 6:00am. You have to retain info of at least 7 successful runs. It's not allowed to run more than one job at the same time. To keep it simple, print the report to the standard output. It's enough to print `This is my report`. Use `busybox` as the base image.

### Solution

Create a cron job yaml using CLI:

```shell
kubectl --dry-run=client -o=yaml -n personal-ns create cronjob reporter-job --image=busybox --schedule="0 6 * * *"  -- echo 'This is my report' > cronjob.yaml
```

Shell

Copy

Since not all properties can be set using CLI, you have to edit the yaml file. Change the cron job to retain the history of 7 last successful runs:

```yaml
successfulJobsHistoryLimit: 7
```

YAML

Copy

Change the concurrency policy to `Forbid` to prevent running multiple jobs at the same time:

```yaml
concurrencyPolicy: Forbid
```

YAML

Copy

Execute the yaml:

```shell
kubectl create -f cronjob.yaml
```

Shell

Copy

------

## Task 8

Use `dk8s` context: `kubectl config use-context dk8s`.

You have joined a new team that is migrating applications to Kubernetes. You have been tasked to create a job that will run a bash script. The script must be sourced from a config map.

You have to create a config map with `/var/resources/08/script.sh` file and then create a job that will run this script from the config map. Use `busybox` as the base image. The job should be named `from-file-job` and the config map should be named `script-configmap`. Create these two objects in `job-ns` namespace.

### Solution

Start with creating `script-configmap`:

```shell
kubectl -n job-ns create configmap script-configmap --from-file=r/var/resources/08/script.sh
```

Shell

Copy

Then generate the base yaml for the job:

```shell
kubectl --dry-run=client -o=yaml create job from-file-job --image=busybox -- sh /scripts/script.sh > job.yaml
```

Shell

Copy

`job.yaml` should contain:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: from-file-job
spec:
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - command:
        - sh
        - /scripts/script.sh
        image: busybox
        name: from-file-job
        resources: {}
      restartPolicy: Never
status: {}
```

YAML

Copy

Edit the file and enhance the configuration by mounting files from `script-configmap` as files in the container:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: from-file-job
spec:
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
        - command:
            - sh
            - /scripts/script.sh
          image: busybox
          name: from-file-job
          resources: {}
          # Mount volume at specific point
          volumeMounts:
            - name: scripts
              mountPath: "/scripts"
              readOnly: true
      restartPolicy: Never
      volumes:
        - name: scripts
          configMap:
            # ConfigMap name
            name: script-configmap
            items:
              - key: "script.sh"
                path: "script.sh"
status: {}
```

YAML

Copy

Apply the yaml:

```shell
kubectl -n job-ns apply -f job.yaml
```

Shell

Copy

To validate the job, check out its logs:

```shell
kubectl -n job-ns logs job/from-file-job
```

Shell

Copy

Desired output:

```shell
Hello World!
This is output from script
End of program. Thank you.
```

Shell

Copy

------

## Task 9

Use `dk8s` context: `kubectl config use-context dk8s`.

You have been asked to create a deployment for an application in `security-ns`. The pod must be run as user id `1001` and group id `1001`. The deployment should be named `tiny-app` and be based on `busybox` image. Use `sleep 3600` as the deployment command.

### Solution

Let's start with creating a base deployment yaml file:

```shell
kubectl -n security-ns create deployment tiny-app --image=busybox --dry-run=client -o=yaml -- sh -c sleep 3600 > /tmp/deployment.yaml
```

Shell

Copy

The deployment yaml should read:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: tiny-app
  name: tiny-app
  namespace: security-ns
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tiny-app
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: tiny-app
    spec:
      containers:
      - image: busybox
        name: busybox
        resources: {}
status: {}
```

YAML

Copy

Now add `securityContext` in `spec` section:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: tiny-app
  name: tiny-app
  namespace: security-ns
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tiny-app
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: tiny-app
    spec:
      containers:
        - image: busybox
          name: busybox
          resources: {}
      # Add Security Context
      securityContext:
        runAsUser: 1001
        runAsGroup: 1001
status: {}
```

YAML

Copy

Apply the yaml:

```shell
kubectl -n security-ns apply -f /tmp/deployment.yaml
```

Shell

Copy

To validate the configuration, run `id` command in the pod:

```shell
kubectl -n security-ns exec deployment/tiny-app -- id
```

Shell

Copy

Expected output:

```shell
uid=1001 gid=1001
```

Shell

Copy

------

## Task 10

Use `dk8s` context: `kubectl config use-context dk8s`.

Your teammate has tried to deploy their application on Kubernetes, but they were not successful. They have asked for your help.

Fix `first-app` deployment in namespace `training-ns` without removing any features.

### Solution

Start by checking what's going on with the deployment:

```shell
kubectl -n training-ns describe deployment first-app
```

Shell

Copy

Output:

```shell
Name:               first-app
Namespace:          training-ns
CreationTimestamp:  Sat, 14 Nov 2020 21:23:48 +0100
Labels:             app=first-app
Annotations:        deployment.kubernetes.io/revision: 1
Selector:           app=first-app
Replicas:           1 desired | 1 updated | 1 total | 0 available | 1 unavailable
StrategyType:       Recreate
MinReadySeconds:    0
Pod Template:
  Labels:  app=first-app
  Containers:
   nginx:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Readiness:    tcp-socket :88 delay=5s timeout=1s period=10s #success=1 #failure=3
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   first-app-56df6f5f8 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  110s  deployment-controller  Scaled up replica set first-app-56df6f5f8 to 1
```

Shell

Copy

We see that there are no available replicas:

```shell
Replicas:           1 desired | 1 updated | 1 total | 0 available | 1 unavailable
```

Shell

Copy

`Readiness` line is reporting failures:

```shell
Readiness:    tcp-socket :88 delay=5s timeout=1s period=10s #success=1 #failure=3
```

Shell

Copy

We have a clue - something is wrong with the readiness probe. Check if the port is available in the deployment pods:

```shell
kubectl -n training-ns exec deployment/first-app -- curl localhost:88
```

Shell

Copy

Output:

```shell
kubectl -n training-ns exec deployment/first-app -- curl localhost:88
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0curl: (7) Failed to connect to localhost port 88: Connection refused
command terminated with exit code 7
```

Shell

Copy

The port is closed. We cannot just remove tbe readiness probe, because we are not allowed to remove any functionality.

Take a deeper look into the deployment description. There is a line that tells us about the exposed port. It is port `80`.

```shell
Port:         80/TCP
```

Shell

Copy

Change the Readiness probe to check `80` port:

```shell
kubectl -n training-ns edit deployment first-app
```

Shell

Copy

Replace:

```yaml
  tcpSocket:
    port: 88
```

YAML

Copy

with

```yaml
  tcpSocket:
    port: 80
```

YAML

Copy

Check the deployment again:

```shell
kubectl -n training-ns describe deployment first-app
```

Shell

Copy

Desired output:

```shell
Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
```

Shell

Copy

------

## Task 11

Use `sk8s` context: `kubectl config use-context sk8s`.

You need to add a container to an existing deployment. The container should run right before the rest of the containers. The deployment is named `printer-app`, and it resides in `print-ns` namespace. The new container should use the `busybox` image and attach a volume named `print-volume` under `/print` path. It must echo `Deployment initialized` to `/print/current.log` file.

### Solution

The task is about adding a init container to an existing deployment. Start with validating the current state:

```shell
kubectl -n print-ns logs deployment/printer-app
```

Shell

Copy

Output:

```shell
File content
cat: can't open '/print/current.log': No such file or directory
File content
cat: can't open '/print/current.log': No such file or directory
File content
cat: can't open '/print/current.log': No such file or directory
```

Shell

Copy

Our init container should create `/print/current.log` file. Let's edit the deployment:

```shell
kubectl -n print-ns edit deployment printer-app
```

Shell

Copy

Add the following section under `spec`:

```yaml
initContainers:
- name: init-container-name
  image: busybox
  command: ['sh', '-c', "echo 'Deployment initialized' > /print/current.log"]
  volumeMounts:
    - name: print-volume
      mountPath: /print
```

YAML

Copy

Let's validate our work:

```shell
kubectl -n print-ns logs deployment/printer-app
```

Shell

Copy

Output:

```shell
File content
Deployment initialized
File content
Deployment initialized
File content
Deployment initialized
File content
```

Shell

Copy

------

## Task 12

Use `sk8s` context: `kubectl config use-context sk8s`.

You work as performance engineer. Your team has created an application and asked you to configure autoscalling.

Your job is to enhance the existing `auto-app` deployment in `auto-ns` namespace with the following autoscaling behavior:

- minimum replicas count is 3
- maximum replicas count is 7
- target average CPU utilization is 50%

### Solution

You need to create a horizontal pod autoscaler for deployment `auto-app`:

```shell
kubectl -n auto-ns autoscale deployment auto-app --cpu-percent=50 --min=3 --max=7
```

Shell

Copy

Since the horizonal pod autoscaler has been configured to require at a minimum 3 instances you should now see 3 pods for `auto-app` deployment. Let's validate:

```shell
kubectl -n auto-ns get po -lapp=auto-app
```

Shell

Copy

You should get 3 pods:

```shell
NAME                        READY   STATUS    RESTARTS   AGE
auto-app-7f7c9c8575-4jvfj   1/1     Running   0          25m
auto-app-7f7c9c8575-fk6b8   1/1     Running   0          17m
auto-app-7f7c9c8575-q876l   1/1     Running   0          17m
```

Shell

Copy

------

## Task 13

Use `sk8s` context: `kubectl config use-context sk8s`.

There is a job configuration that generates a report and appends it to `/reports/output.txt` file. For now, the report is lost when a job pod terminates. Your task is to introduce persistence.

Create a persistent volume claim named `report-pvc` and configure it to be mounted under`/reports` path in `report-job`. The job definition is in `/var/resources/13/job.yaml` file. After you have added the persistent volume to the job definition, run the job in Kubernetes.

The persistent volume should have the following parameters:

- Size: 100M
- Access Mode: ReadWriteOnce
- Storage Class name: manual

### Solution

Create a persistent volume claim. There is no `create` command for it. It has to be done with a yaml file:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: reports-pvc
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100M
```

YAML

Copy

Apply it with:

```shell
kubectl -n reports-ns apply -f <yaml-file>
```

Shell

Copy

Let's plug the newly created volume into the job. Edit `/var/resources/13/job.yaml` file and add the following section next to `specs`:

```yaml
volumes:
- name: reports-volume
  persistentVolumeClaim:
    claimName: reports-pvc
```

YAML

Copy

Also, add `volumeMounts` under `busybox` container:

```yaml
volumeMounts:
  - mountPath: "/reports"
    name: reports-volume
```

YAML

Copy

The final configuration should look like this:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: report-job
  namespace: reports-ns
spec:
  template:
    spec:
      containers:
        - name: busybox
          image: busybox
          command: ["sh",  "-c", "echo 'This is a report' > /reports/output.txt; echo 'Report generated'"]
          volumeMounts:
            - mountPath: "/reports"
              name: reports-volume
      restartPolicy: Never
      volumes:
        - name: reports-volume
          persistentVolumeClaim:
            claimName: reports-pvc
```

YAML

Copy

Let's run the job:

```shell
kubectl -n reports-ns apply -f resources/job.yaml
```

Shell

Copy

Validate that it has completed successfully:

```shell
kubectl -n reports-ns describe job report-job
```

Shell

Copy

It should contain `1 Succeeded` description:

```shell
Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
```

Shell

Copy

Let's validate the contents of `output.txt`. For that, we will need to run a new pod. Let's start with:

```shell
kubectl -n reports-ns run temporary-pod --dry-run=client  -o yaml --image=busybox -- cat /reports/output.txt > /tmp/pod.yaml
```

Shell

Copy

Output:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: temporary-pod
  name: temporary-pod
spec:
  containers:
  - args:
    - cat
    - /reports/output.txt
    image: busybox
    name: temporary-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

YAML

Copy

Now let's add the volume:

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: temporary-pod
  name: temporary-pod
spec:
  containers:
    - args:
        - cat
        - /reports/output.txt
      image: busybox
      name: temporary-pod
      resources: {}
      # mount Volume to Pod
      volumeMounts:
        - mountPath: "/reports"
          name: reports-volume
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  # Add mapping for PVC
  volumes:
    - name: reports-volume
      persistentVolumeClaim:
        claimName: reports-pvc
status: {}
```

YAML

Copy

Then apply configuration:

```shell
kubectl -n reports-ns apply -f /tmp/pod.yaml
```

Shell

Copy

Finally, read the logs of the temporary pod:

```shell
kubectl -n reports-ns logs pod/temporary-pod
```

Shell

Copy

Output:

```shell
This is a report
```

Shell

Copy

------

## Task 14

Use `sk8s` context: `kubectl config use-context sk8s`.

Your company has come up with a requirement to run all deployments using a different Service Account than the default one. You have been asked to create a new service account named `new-user` and then use it to run `account-app` deployment in `account-ns` namespace.

### Solution

Start by creating the service account:

```shell
kubectl -n account-ns create serviceaccount new-user
```

Shell

Copy

Then edit the existing deployment:

```shell
kubectl -n account-ns edit deployment/account-app
```

Shell

Copy

Add the following line in `spec`:

```yaml
...
    spec:
      containers:
      - image: nginx:1.14.2
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      # Configure Service Account here
      serviceAccountName: new-user
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
...
```

YAML

Copy

Save changes and wait for redeployment. Verify if it went ok:

```shell
kubectl -n account-ns describe deployment account-app
```

Shell

Copy

Output:

```shell
Name:                   account-app
Namespace:              account-ns
CreationTimestamp:      Mon, 16 Nov 2020 21:32:01 +0100
Labels:                 app=account-app
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=account-app
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:           app=account-app
  Service Account:  new-user
  Containers:
   nginx:
    Image:        nginx:1.14.2
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   account-app-7f77d8965 (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  9m42s  deployment-controller  Scaled up replica set account-app-5cb7f7459c to 1
  Normal  ScalingReplicaSet  6m45s  deployment-controller  Scaled up replica set account-app-7f77d8965 to 1
  Normal  ScalingReplicaSet  6m43s  deployment-controller  Scaled down replica set account-app-5cb7f7459c to 0
```

Shell

Copy

As you can see, `new-user` is now used to run pods in the deployment:

```shell
Service Account:  new-user
```

Shell

Copy

------

## Task 15

Use `nk8s` context: `kubectl config use-context nk8s`.

Your company is starting its cloud journey. You have been asked to create a deployment for one of your applications.

The deployment should be named `start-app` and exist in `start-ns` namespace. You will be deploying `nginx` image. The deployment should have 3 replicas. Each pod in this deployment must be labelled: `department=my_department`. Use `recreate` deployment strategy.

Create a command that shows deployment events. Save the command to to `/var/answers/deployment_events_cmd.yaml`. Make sure that the command captures events only for `start-app` deployment.

### Solution

Let's generate the initial yaml for `start-app` deployment:

```shell
kubectl --dry-run=client -o yaml -n start-ns create deployment start-app --image=nginx --replicas=3 > /tmp/deployment.yaml
```

Shell

Copy

Output:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: start-app
  name: start-app
  namespace: start-ns
spec:
  replicas: 3
  selector:
    matchLabels:
      app: start-app
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: start-app
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

YAML

Copy

Change the deployment strategy to `Recreate`. Find:

```yaml
  strategy: {}
```

YAML

Copy

and change it to:

```yaml
strategy:
  type: Recreate
```

YAML

Copy

Add a new label to the template:

```yaml
...
template:
  metadata:
    creationTimestamp: null
    labels:
      department: my_department
      app: start-app
...
```

YAML

Copy

Now apply the deployment configuration:

```shell
kubectl -n start-ns apply -f /tmp/deployment.yaml
```

Shell

Copy

Let's now deal with the second part of the problem. Let's see what `get events` command generates:

```shell
kubectl -n start-ns get events
```

Shell

Copy

Output:

```shell
LAST SEEN   TYPE     REASON              OBJECT                            MESSAGE
7m46s       Normal   Scheduled           pod/start-app-55b87687fb-7q9dk    Successfully assigned start-ns/start-app-55b87687fb-7q9dk to minikube
...
14m         Normal   Started             pod/start-app-75cd8d7467-x7647    Started container nginx
7m56s       Normal   Killing             pod/start-app-75cd8d7467-x7647    Stopping container nginx
15m         Normal   SuccessfulCreate    replicaset/start-app-75cd8d7467   Created pod: start-app-75cd8d7467-x7647
14m         Normal   SuccessfulCreate    replicaset/start-app-75cd8d7467   Created pod: start-app-75cd8d7467-lf7nk
14m         Normal   SuccessfulCreate    replicaset/start-app-75cd8d7467   Created pod: start-app-75cd8d7467-sqhm9
7m56s       Normal   SuccessfulDelete    replicaset/start-app-75cd8d7467   Deleted pod: start-app-75cd8d7467-sqhm9
7m56s       Normal   SuccessfulDelete    replicaset/start-app-75cd8d7467   Deleted pod: start-app-75cd8d7467-lf7nk
7m56s       Normal   SuccessfulDelete    replicaset/start-app-75cd8d7467   Deleted pod: start-app-75cd8d7467-x7647
15m         Normal   ScalingReplicaSet   deployment/start-app              Scaled up replica set start-app-75cd8d7467 to 1
14m         Normal   ScalingReplicaSet   deployment/start-app              Scaled up replica set start-app-75cd8d7467 to 3
7m56s       Normal   ScalingReplicaSet   deployment/start-app              Scaled down replica set start-app-75cd8d7467 to 0
7m46s       Normal   ScalingReplicaSet   deployment/start-app              Scaled up replica set start-app-55b87687fb to 3
...
```

Shell

Copy

As you can see all events are printed. We need to filter only `start-app` deployment events. We can use `--field-selector`. We need to find out what field to filter on. Let's get a more detailed view of events:

```shell
kubectl get events -o yaml:
```

Shell

Copy

The output will contain:

```yaml
...
- apiVersion: v1
  count: 1
  eventTime: null
  firstTimestamp: "2020-11-18T12:21:30Z"
  involvedObject:
    apiVersion: apps/v1
    kind: Deployment
    name: start-app
    namespace: start-ns
    resourceVersion: "517157"
    uid: 2abb743f-ae3c-40e2-b49b-8b16d6aba3e7
  kind: Event
  lastTimestamp: "2020-11-18T12:21:30Z"
  message: Scaled up replica set start-app-55b87687fb to 3
...
```

YAML

Copy

Based on this output, the field selector for `start-app` deployment will be: `involvedObject.name=start-app`. Let's try it out:

```shell
kubectl -n start-ns get events --field-selector='involvedObject.name=start-app'
```

Shell

Copy

Output:

```shell
LAST SEEN   TYPE     REASON              OBJECT                            MESSAGE
15m         Normal   ScalingReplicaSet   deployment/start-app              Scaled up replica set start-app-75cd8d7467 to 1
14m         Normal   ScalingReplicaSet   deployment/start-app              Scaled up replica set start-app-75cd8d7467 to 3
7m56s       Normal   ScalingReplicaSet   deployment/start-app              Scaled down replica set start-app-75cd8d7467 to 0
7m46s       Normal   ScalingReplicaSet   deployment/start-app              Scaled up replica set start-app-55b87687fb to 3
```

Shell

Copy

We have got the desired output. Save the command to the target file:

```shell
echo "kubectl -n start-ns get events --field-selector='involvedObject.name=start-app'" > /var/answers/deployment_events_cmd.txt
```

Shell

Copy

------

## Task 16

Use `nk8s` context: `kubectl config use-context nk8s`.

You are a fresh SRE engineer in your new company. You have got your first task: upgrade the image version for `to-update-app` deployment in `update-ns` namespace from `1.18` to `1.19`. After that make sure that the new version is deployed. Fix any issues.

### Solution

Edit `to-update-app` deployment and set the new image version:

```shell
kubectl -n update-ns edit deployment to-update-app
```

Shell

Copy

Change:

```yaml
- image: nginx:1.18
```

YAML

Copy

to:

```yaml
- image: nginx:1.19
```

YAML

Copy

After that, verify that the deployment includes the correct version of the image. Let's list all pods for `to-update-app` deployment:

```shell
kubectl -n update-ns get pods -l=app=to-update-app
```

Shell

Copy

Output:

```shell
NAME                            READY   STATUS    RESTARTS   AGE
to-update-app-658944754-v7mnw   1/1     Running   0          13m
```

Shell

Copy

Get the details:

```shell
kubectl -n update-ns describe pod to-update-app-658944754-v7mnw
```

Shell

Copy

Output:

```shell
Name:         to-update-app-658944754-v7mnw
Namespace:    update-ns
Priority:     0
Node:         minikube/192.168.99.100
Start Time:   Fri, 20 Nov 2020 21:15:04 +0100
Labels:       app=to-update-app
              pod-template-hash=658944754
Annotations:  <none>
Status:       Running
IP:           172.17.0.8
IPs:
  IP:           172.17.0.8
Controlled By:  ReplicaSet/to-update-app-658944754
Containers:
  nginx:
    Container ID:   docker://bde6a69a41dc5d1e3c5ac560dac4521eef3f4da8a089720fac5847737b77692f
    Image:          nginx:1.18
    Image ID:       docker-pullable://nginx@sha256:2104430ec73de095df553d0c7c2593813e01716a48d66f85a3dc439e050919b3
    Port:           <none>
    Host Port:      <none>
...
```

Shell

Copy

As you can see, it's still using the old image. Let's check the deployment:

```shell
kubectl -n update-ns describe deployment to-update-app
```

Shell

Copy

Output:

```shell
Conditions:
  Type           Status   Reason
  ----           ------   ------
  Available      True     MinimumReplicasAvailable
  Progressing    Unknown  DeploymentPaused
OldReplicaSets:  to-update-app-658944754 (1/1 replicas created)
```

Shell

Copy

It turns out that the deployment has been paused. Resume it:

```shell
kubectl -n update-ns rollout resume deployment/to-update-app
```

Shell

Copy

Check the image version once again. Get the pod name:

```shell
kubectl -n update-ns get pods -l=app=to-update-app
```

Shell

Copy

Output:

```shell
NAME                             READY   STATUS    RESTARTS   AGE
to-update-app-7bcf789fd5-vx49c   1/1     Running   0          75s
```

Shell

Copy

Retrieve pod details:

```shell
kubectl -n update-ns describe pod to-update-app-7bcf789fd5-vx49c
```

Shell

Copy

Output:

```shell
Name:         to-update-app-7bcf789fd5-vx49c
Namespace:    update-ns
Priority:     0
Node:         minikube/192.168.99.100
Start Time:   Fri, 20 Nov 2020 21:34:00 +0100
Labels:       app=to-update-app
              pod-template-hash=7bcf789fd5
Annotations:  <none>
Status:       Running
IP:           172.17.0.10
IPs:
  IP:           172.17.0.10
Controlled By:  ReplicaSet/to-update-app-7bcf789fd5
Containers:
  nginx:
    Container ID:   docker://a46955e0682f703c02a1da7f0a3efb1ebd77cd676e1e6c9ceb052618cabab8fb
    Image:          nginx:1.19
...
```

Shell

Copy

The image is now in version `1.19` as expected.

------

## Task 17

Use `nk8s` context: `kubectl config use-context nk8s`.

You need to create a service for an existing deployment called `server-app`. The deployment is in `server-ns` namespace. Name the newly created service `server-service`. Add an annotation: `type=testing`. The service must route traffic from port `8080` to port `80`. The service must allocate a static port on each node.

Create a command that prints the node port and save the command to `/var/answers/static_port.txt` file. The output should be only the number of the port, nothing else.

### Solution

Since the service must use a static port on Kubernetes nodes we have to use `NodePort` service type. The easiest way for exposing the deployment is:

```shell
kubectl -n server-ns expose deployment/server-app --port=8080 --target-port=80 --name=server-service --type=NodePort
```

Shell

Copy

Now, let's add the annotation:

```shell
kubectl -n server-ns annotate service server-service type=testing
```

Shell

Copy

Finally, we need to create a command that retrieves the node port value. Let's retrieve the service information in json format:

```shell
kubectl -n server-ns get service server-service -o json
```

Shell

Copy

Output:

```json
{
    "apiVersion": "v1",
    "kind": "Service",
    "metadata": {
        "annotations": {
            "type": "testing"
        },
        "creationTimestamp": "2020-11-21T20:00:28Z",
        "labels": {
            "app": "server-app"
        },
        "name": "server-service",
        "namespace": "server-ns",
        "resourceVersion": "631113",
        "selfLink": "/api/v1/namespaces/server-ns/services/server-service",
        "uid": "85f35f04-35b3-4285-97ef-2c31c4ca1def"
    },
    "spec": {
        "clusterIP": "10.107.167.80",
        "externalTrafficPolicy": "Cluster",
        "ports": [
            {
                "nodePort": 30998,
                "port": 8080,
                "protocol": "TCP",
                "targetPort": 80
            }
        ],
        "selector": {
            "app": "server-app"
        },
        "sessionAffinity": "None",
        "type": "NodePort"
    },
    "status": {
        "loadBalancer": {}
    }
}
```

JSON

Copy

As you can see, the allocated port is listed in `.spec.ports` under `nodePort` element. We can retrieve the value using `jsonpath`. Since `ports` is an array, we will use a wildcard to avoid hardcoding the element position:

```shell
kubectl -n server-ns get service server-service -o jsonpath='{.spec.ports[*].nodePort}'
```

Shell

Copy

Output:

```json
30998
```

JSON

Copy

Finally, save the command to the target file:

```shell
echo "kubectl -n server-ns get service server-service -o jsonpath='{.spec.ports[*].nodePort}'" > /var/answers/static_port.txt
```

Shell

Copy

------

## Task 18

Use `nk8s` context: `kubectl config use-context nk8s`.

You are an SRE engineer who works on `api-app` application. This and other apps are deployed on Kubernetes. This company has strict network security rules: all connectivity is forbidden by default. Your app is deployed to `api-ns` namespace and other team who owns `allowed-ns` needs to connect to `api-app` service and other services in your namespace. Specifically, each container from `allowed-ns` namespace should be able to connect to all assets deployed in `api-ns` namespace.

Please configure a network policy called `allow-traffic` in `api-ns` namespace to allow all incoming traffic from `allowed-ns` namespace to `api-ns` namespace. You can use `api-app` service to test the policy. The service accessible at `http://api-app.api-ns:80`.

### Solution

In this task we need to create a network policy object. There is no generator for it, so we need to create a yaml configuration from scratch.

We need to allow all traffic from `allowed-ns` into `api-ns` namespace. Fortunately, network policies can be configured at namespace level using labels.

Let's check labels for both `allowed-ns` and `not-allowed-ns`, because this is how we specify which namespace is allowed to connect:

```shell
kubectl get namespace allowed-ns --show-labels
```

Shell

Copy

Output:

```shell
NAME         STATUS   AGE   LABELS
allowed-ns   Active   37m   <none>
```

Shell

Copy

Now, let's check `not-allowed-ns` namespace:

```shell
kubectl get namespace not-allowed-ns --show-labels
```

Shell

Copy

Output:

```shell
NAME         STATUS   AGE   LABELS
not-allowed-ns   Active   37m   <none>
```

Shell

Copy

There are no labels in both the cases. Let's come up with one and add it to `allowed-ns`:

```shell
kubectl label namespace/allowed-ns api=allowed
```

Shell

Copy

Now that the label has been set up, let's prepare a network policy configuration in `/tmp/network_policy.yaml` file:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: allow-traffic
spec:
  podSelector:
    matchLabels: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          api: allowed
```

YAML

Copy

Apply the yaml:

```shell
kubectl -n api-ns apply -f /tmp/network_policy.yaml
```

Shell

Copy

Let's validate the connectivity by running a `wget` against the service in a temporary `busybox` container in `allowed-ns`:

```shell
kubectl run busybox-test -n allowed-ns --image=busybox -it --rm --restart=Never -- wget -qO- -T 3 http://api-app.api-ns
```

Shell

Copy

You should see:

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
pod "busybox-test" deleted
```

HTML

Copy

------

## Task 19

Use `nk8s` context: `kubectl config use-context nk8s`.

There is `web-app` deployment running in `app-ns` namespace. It's a `nginx` based web server. Please expose a service for this deployment that will allow external traffic to the web server. The service should expose port `9090`. Name the service `app-service`.

### Solution

Let's check what ports are defined in `web-app` deployment:

```shell
kubectl -n app-ns describe deployment web-app
```

Shell

Copy

Output:

```shell
Name:                   web-app
Namespace:              app-ns
CreationTimestamp:      Sat, 28 Nov 2020 18:00:57 +0100
Labels:                 app=web-app
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=web-app
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=web-app
  Containers:
   nginx:
    Image:        nginx:1.19
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   web-app-7769fc5499 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  20s   deployment-controller  Scaled up replica set web-app-7769fc5499 to 1
```

Shell

Copy

As you can see, there is only one container that exposes `80` port. This port should be used in the service configuration. Let's expose the service:

```shell
kubectl -n app-ns expose deployment web-app --port=9090 --target-port=80 --name=app-service
```

Shell

Copy

Now, let's validate the connectivity. To make it more interesting, let's connect to the service from `other-ns` namespace. Since we are going across namespaces, we should use `<service-name>.<service-namespace>:<service-port>` as the service address.

Start a pod in `other-ns` namespace and execute `wget` against the service. It's important to include `-T` timeout parameter. This will help you recover quickly in case the service has not been exposed properly and there is no connectivity.

```shell
kubectl run busybox-test -n other-ns --image=busybox -it --rm --restart=Never -- wget -qO- -T 3 http://app-service.app-ns:9090
```

Shell

Copy

You should see:

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
pod "busybox-test" deleted
```

HTML

Copy
