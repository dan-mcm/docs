# Kubernetes

Notes based on practice following the Udemy course: [Learn DevOps: The Complete Kubernetes Course](https://www.udemy.com/learn-devops-the-complete-kubernetes-course/).

See related GitHub code files [here](https://github.com/daniel40392/kubernetes-course).

<p align="center">
  <img src="https://media.giphy.com/media/991T0LFIrKGfS/giphy.gif"/>
</p>

### Vagrant Commands

To build a plain ubuntu box
```
mkdir ubuntu
vagrant init ubuntu/xenial64
vagrant up
```
<br/>
### Docker Commands

The -it flag will allow you to ctrl+c to close the running image
`docker run -p 3000:3000 -it 0319d6e6a683`

The -d flag will run service in the background allowing continued terminal access
`docker run -p 3000:3000 -d 0319d6e6a683`

To push an image to Docker Hub (required for Kubernetes access)
```
docker login
docker tag imageid your-login/docker-demo
docker push your-login/docker-demo
```

To tag an image during the build process
```
docker build -t your-login/docker-demo .
docker push your-login/docker-demo
```
<br/>
### Running Docker Image on Kubernetes

- Before launching container based on Docker image need to create pod definition
- Pod: describes an application running on Kubernetes
- Service: contain one or more tightly coupled containers (that make up the app - easily communicate with local port numbers)

<br/>
#### Setup Application on Kubernetes

- `kubectl create -f k8s/demopod-helloworld.yml`
- `kubectl describe pod nodehelloworld.example.com`
- `kubectl port-forward nodehelloworld.example.com 8081:3000`
- `kubectl expose pod nodehelloworld.example.com --type=NodePort --name=helloworld-service`
- `minikube service helloworld-service --url` returns url of the service running
= `kubectl get service`

<br/>
#### Useful Kubectl Commands (tie in with above example)

- `kubectl attach helloworld.example.com`
- `kubectl exec helloworld.example.com -- ls /app` lists file running inside container
- `kubectl exec helloworld.example.com -- touch /app/test.txt` creates file inside container  (will disappear if the container is killed - non-persistent data)
- `kubectl describe service helloworld-service`
- `kubectl run -i -tty busybox --image=busybox --restart=Never -- sh`

<br/>
#### Useful Generic Pod Commands

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

<br/>
### Kops Cluster Deployment

AFTER SSHING INTO LINUX INSTANCE (vagrant via putty):

*setup awscli*
```
sudo apt-get install python-pip
sudo pip install awscli
aws configure (use IAM role details - with admin policy)
ls -ahl ~/.aws/ (verify credentials)
```

*setup kubectl*
```
wget https://storage.googleapis.com/kubernetes-release/release/v1.6.1/bin/linux/amd64/kubectl
sudo mv kubectl /usr/local/bin/
sudo chmod +x /usr/local/bin/kubectl
kubectl
```

*setup keygen*
```
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
```
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

<br/>
### Horizontal -v- Vertical Scaling

You can only horizontally scale when your pod is stateless. (i.e. kubectl scale options).
Stateful pods cannot be horizontally scaled.
