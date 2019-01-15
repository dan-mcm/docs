# Kubernetes

Notes based on practice following the Udemy course: [Learn DevOps: The Complete Kubernetes Course](https://www.udemy.com/learn-devops-the-complete-kubernetes-course/).

See related GitHub code files [here](https://github.com/wardviaene/kubernetes-course).

<p align="center">
  <img src="https://media.giphy.com/media/991T0LFIrKGfS/giphy.gif"/>
</p>

## Vagrant Commands

To build a plain ubuntu box
```bash
mkdir ubuntu
vagrant init ubuntu/xenial64
vagrant up
```

## Running Docker Container on Kubernetes

- Before launching container based on Docker image need to create pod definition
- Pod: describes an application running on Kubernetes
- Service: contain one or more tightly coupled containers (that make up the app - easily communicate with local port numbers)

### Setup Application on Kubernetes

- `kubectl create -f k8s/demopod-helloworld.yml`
- `kubectl describe pod nodehelloworld.example.com`
- `kubectl port-forward nodehelloworld.example.com 8081:3000`
- `kubectl expose pod nodehelloworld.example.com --type=NodePort --name=helloworld-service`
- `minikube service helloworld-service --url` returns url of the service running
= `kubectl get service`

### Useful Kubectl Commands (tie in with above example)

- `kubectl attach helloworld.example.com`
- `kubectl exec helloworld.example.com -- ls /app` lists file running inside container
- `kubectl exec helloworld.example.com -- touch /app/test.txt` creates file inside container  (will disappear if the container is killed - non-persistent data)
- `kubectl describe service helloworld-service`
- `kubectl run -i -tty busybox --image=busybox --restart=Never -- sh`

## Kops Cluster Deployment

AFTER SSHING INTO LINUX INSTANCE (vagrant via putty):

*setup awscli*
```bash
sudo apt-get install python-pip
sudo pip install awscli
aws configure (use IAM role details - with admin policy)
ls -ahl ~/.aws/ (verify credentials)
```

*setup kubectl*
```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.6.1/bin/linux/amd64/kubectl
sudo mv kubectl /usr/local/bin/
sudo chmod +x /usr/local/bin/kubectl
kubectl
```

*setup keygen*
```bash
ssh-keygen -f .ssh/id_rsa
cat .ssh/id_rsa.pub
```

*rename kops installation*
`sudo mv /usr/local/bin/kops-linux-amd64 /usr/local/bin/kops`

*generate cluster with your s3 bucket with route53 dns zone*
`kops create cluster --name=kubernetes.yourowndomain.com --state=s3://kops-state-randomhash --zones=eu-west-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.yourowndomain.com`

`kops update cluster kubernetes.yourowndomain.com --yes --state=s3://kops-state-randomhash`

*check your cert and password to log into new cluster*
`cat .kube/config`

*check nodes are running*
`kubectl get node`

*test own program on nodes*
```bash
kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
kubectl expose deployment hello-minikube --type=NodePort
kubectl get service
```

at this point add custom rule to aws- using VPC dashbaord - select master node - modify inbound traffic - all allowed on the port of your app in this case: 31956
Test it out: http://api.kubernetes.yourowndomain.com:31956/

*to delete and avoid payments*
`kops delete cluster --name kubernetes.yourowndomain.com --state=s3://kops-state-randomhash`

*to agree to delete*
`kops delete cluster --name kubernetes.yourowndomain.com --state=s3://kops-state-randomhash --yes`

## Horizontal -v- Vertical Scaling

You can only horizontally scale when your pod is stateless. (i.e. kubectl scale options).
Stateful pods cannot be horizontally scaled.

## Useful Pod Commands

- `kubectl get pod` Get info about all running pods
- `kubectl describe pod <pod>` Describe one pod
- `kubectl expose pod <pod> --port=444 --name=frontend` Expose port of a pod (creates new service)
- `kubectl port-forward <pod> 8080` Port forward teh exposed pod port to your local machine
- `kubectl attach <podname> -i` Attach to the pod
- `kubectl exec <pod> --command` Execute a command on the pod
- `kubectl label pods <pod> mylabel=awesome` Add a new label to a pod
- `kubectl run -i --tty busybox --image=busybox --restart=Never -- sh` Run a shell in a pod - very useful for debugging. Once in you can run `telnet 172.17.0.5 3000` to hit service and then command `GET /`
- `kubectl scale --replicas=4 -f file.yaml` Scales the number of pods replicated
- `kubectl get rc` Gets replica controllers
- `kubectl scale --replicas=4 -f rc/helloworld-controller` Scales the number of pods replicated
- `kubectl delete rc/helloworld-controller` Deletes controller

## Useful Deployment Commands

* `kubectl get deployments`
* `kubectl get rs`
* `kubectl get pods --show-labels`
* `kubectl rollout status deployment/helloworld-deployment`
* `kubectl set image deployment/helloworld-deployment k8s-demo=k8s-demo:2` can be used to update app to latest version
* `kubectl edit deployment/helloworld-deployment`
* `kubectl rollout history deployment/helloworld-deployment`
* `kubectl rollout undo deployment/helloworld-deployment --to-revision=n`

## Useful Service Commands

* ClusterIP - virtual IP address only reachable from within the Cluster (default)
* NodePort - same on each node also reachable externally
* LoadBalancer - routes external traffic to every node on the NodePort (ELB on AWS)
* ExternalName can provide a DNS name for service (e.g. service discovery using DNS) - only works when DNS add-on is enabled
* By default service can only run between ports 30000-32767 but can be changed by adding --service-node-port-range= argument to kube-apiserver

* `minikube service service-name --url`
* `kubectl describe svc helloworld-service`
* `kubectl get svc` svc short for service

## Labels

* key/value pairs attached to objects
* labels are not unique, multiple can be added to one objects
* Label selectors - you can use matching expressions to match labels
* `kubectl get nodes --show-labels`

### Node labels

* You can also use labels to tag nodes - once tagged you can use label selectors to let pods only run on specific nodes
* 2 steps to run a pod on a specific set of nodes:
  * First you tag the node
  * Then you add a nodeSelector to your pod configuration

First step: add a label or multiple to your nodes:
```bash
kubectl label nodes node1 hardware=high-spec
kubectl label nodes node2 hardware=low=spec
```

Second step: add a pod that uses those labels:
```yaml
apiVersion: v1
kind: pod
metadata:
  name: nodehelloworld.example.com
  labels:
    app: helloworld
spec:
  containers:
  - name: k8s-demo
  image: daniel40392/k8s-demo
  ports:
  - containerPort: 3000
nodeSelector:
  hardware: high-spec
```

## Health Checks

To detect and resolve problems with you app, you can run health checks

2 types of healthchecks:
* Running a command in the container periodically
* Periodic checks on a URL (HTTP)

```yaml
apiVersion: v1
kind: pod
metadata:
  name: nodehelloworld.example.com
  labels:
    app: helloworld
spec:
  containers:
  - name: k8s-demo
  image: daniel40392/k8s-demo
  ports:
  - containerPort: 3000
  livenessProbe:
    httpGet:
      path: /
      port: 3000
    initialDelaySeconds: 15
    timeoutSeconds: 30
```

You can describe pods to see the liveness in effect and success/failure counts

## Readiness Probes

* livenessProbes: indicates whether container is Running

* readinessProbes: indicates whether container is ready to serve
  * make sure at startup the pod will onyl receive traffic when test succeeds
  * you can use them in conjunction

## Pod State

* Pod Status `kubectl get pods` [Pending/Succeeded/Failed/Unknown]
* Pod Condition `kubectl describe pod PODNAME` [PodScheduled/Ready/Initialized/Unschedulable/ContainersReady]
* Container State `kubectl get pod <podname>`[Running/Terminated/Waiting]

## Secrets

* A way to distribute credentials, keys, passwords or data to pods

Use in the following ways:

* Use secrets as env varaiables
* Use secrets as a file in a pod
  * Uses volumes to be mounted in a container with files, can be used for dotenv files or your app can just read this file.
* Use an external image to pull secrets from a private image registry

To generate secrets using files:

```bash
echo -n "root" > ./username.txt
echo -n "password" > ./password.txt
kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
```

Can also be an SSH key or SSL cert:
```bash
kubectl create secret generic ssl-certificate --from-file=ssh-privatekey=~/.sshid_rsa --ssl-cert=mysslcert.crt
```

To generate secrets using yaml

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: blergh # base64 encoded
  username: blergh2 # base64 encoded
```

```bash
echo -n "blergh" | base64
```


### Using Secrets

Create a pod that exposes secrets as env variables:

```yaml
apiVersion: v1
kind: pod
metadata:
  name: nodehelloworld.example.com
  labels:
    app: helloworld
spec:
  containers:
  - name: k8s-demo
  image: daniel40392/k8s-demo
  ports:
  - containerPort: 3000
  env:
    - name: SECRET_USERNAME
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: username
    - name: SECRET_PASSWORD
    [...]
```

Alternatively you can provide the secrets in a file:

```yaml
apiVersion: v1
kind: pod
metadata:
  name: nodehelloworld.example.com
  labels:
    app: helloworld
spec:
  containers:
  - name: k8s-demo
  image: daniel40392/k8s-demo
  ports:
  - containerPort: 3000
  volumeMounts:
  - name: credvolume
    mountPath: /etc/creds
    readOnly: true
  volumes:
  - name: credvolume
  secret:
    secretName: db-secrets
```
