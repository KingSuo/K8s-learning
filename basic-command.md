# Kubernetes Basics
官网：  https://kubernetes.io/docs/tutorials/kubernetes-basics/

---

## Create a Cluster
```kube
# 查看minikube版本
minikube version
# 创建一个单集群
minikube start
# see kubectl version
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.0", GitCo
mmit:"fc32d2f3698e36b93322a3465f63a14e9f0eaead", GitTreeState:"clean", BuildDat
e:"2018-03-26T16:55:54Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.0", GitCo
mmit:"fc32d2f3698e36b93322a3465f63a14e9f0eaead", GitTreeState:"clean", BuildDat
e:"2018-04-10T12:46:31Z", GoVersion:"go1.9.4", Compiler:"gc", Platform:"linux/a
md64"}

# view the cluster details
kubectl cluster-info
$ kubectl cluster-info
Kubernetes master is running at https://172.17.0.59:8443

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

# get nodes
kubectl get nodes
$ kubectl get nodes
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     <none>    3m        v1.10.0
```

## Deploy an App
```kube
# run an app on kubernetes
kuberctl run (deployment_name) --image=(image full repository url) --port=(specific port by you select)

$ kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernets-bootcamp:v1 --port=8080

$ deployment.apps "kubernetes-bootcamp" created

# list deployments
$ kubectl get deployments
NAME                  DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGEkubernetes-bootcamp   1         1         1            0           6m

# list pods
kubectl get pods
$ kubectl get pods
NAME                                   READY     STATUS             RESTARTSAGEkubernetes-bootcamp-84f46546c7-gvd22   0/1       ImagePullBackOff   07m

# create a proxy
$ kubectl proxy
# The kubectl command can create a proxy that will forward communications into the cluster-wide, private network. The proxy can be terminated by pressing control-C and won't show any output while its running.

$ curl http://localhost:8001/version{
  "major": "1",
  "minor": "10",
  "gitVersion": "v1.10.0",
  "gitCommit": "fc32d2f3698e36b93322a3465f63a14e9f0eaead",
  "gitTreeState": "clean",
  "buildDate": "2018-04-10T12:46:31Z",  "goVersion": "go1.9.4",
  "compiler": "gc",
  "platform": "linux/amd64"
}

# get and save pod name
$ export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
$ echo Name of the Pod: $POD_NAME
Name of the Pod: kubernetes-bootcamp-5c69669756-gxm48

# make http request
$ curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-5c69669756-xmb55 | v=1
```

## Explore Your App
```kube
# list pods
$ kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
kubernetes-bootcamp-5c69669756-fbxqm   1/1       Running   0          17m

# describe pods

$ kubectl describe pods
Name:           kubernetes-bootcamp-5c69669756-fbxqm
Namespace:      default
Node:           minikube/172.17.0.43
Start Time:     Wed, 01 Aug 2018 16:49:52 +0000
Labels:         pod-template-hash=1725225312
                run=kubernetes-bootcamp
Annotations:    <none>
Status:         Running
IP:             172.18.0.2
Controlled By:  ReplicaSet/kubernetes-bootcamp-5c69669756
Containers:
  kubernetes-bootcamp:
    Container ID:   docker://301c579037b1af5430cf3c3a776a751a29d7d36d46fb06b26027b6ee7c684230
    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
    Image ID:       docker-pullable://gcr.io/google-samples/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 01 Aug 2018 16:49:53 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-pbjwz (ro)
Conditions:
  Type           Status
  Initialized    True
  Ready          True
  PodScheduled   True
Volumes:
  default-token-pbjwz:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-pbjwz
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason                 Age                From               Message
  ----     ------                 ----               ----               -------
  Warning  FailedScheduling       18m (x4 over 18m)  default-scheduler  0/1 nodes are available: 1 node(s) were not ready.
  Normal   Scheduled              18m                default-scheduler  Successfully assigned kubernetes-bootcamp-5c69669756-fbxqm to minikube
  Normal   SuccessfulMountVolume  18m                kubelet, minikube  MountVolume.SetUp succeeded for volume "default-token-pbjwz"
  Normal   Pulled                 18m                kubelet, minikube  Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal   Created                18m                kubelet, minikube  Created container
  Normal   Started                18m                kubelet, minikube  Started container

# retrieve logs
$ kubectl logs $POD_NAMEKubernetes Bootcamp App Started At: 2018-08-01T16:49:53.651Z | Running On:  kub
ernetes-bootcamp-5c69669756-fbxqm
Running On: kubernetes-bootcamp-5c69669756-fbxqm | Total Requests: 1 | App Uptime: 1711.617 seconds | Log Time: 2018-08-01T17:18:25.268Z

# execute commands on container
$ kubectl exec $POD_NAME envPATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/binHOSTNAME=kubernetes-bootcamp-5c69669756-fbxqm
KUBERNETES_PORT_443_TCP_PORT=443KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
NPM_CONFIG_LOGLEVEL=infoNODE_VERSION=6.3.1
HOME=/root

# execute start in a bash
$ kubectl exec -ti $POD_NAME bash
root@kubernetes-bootcamp-5c69669756-fbxqm:/#

# ep
root@kubernetes-bootcamp-5c69669756-fbxqm:/# curl localhost:8080
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-5c69669756-fbxqm | v=1
```