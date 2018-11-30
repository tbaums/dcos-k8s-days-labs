# Lab 5 - Deploy an App from Image Repo

### Use the Kubernetes-cluster2 K8s Dashboard

Ensure that you have connectivity to Kubernetes-cluster2 by pointing your browser to:

http://127.0.0.1:8002/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

If the above URL does not work, then the kubectl proxy needs to be restarted.  We need to ensure that we have selected the kubernetes-cluster2.  To do this:

Kubectl can only control one Kubernetes cluster at a time.  We need to ensure that we have selected the kubernetes-cluster2.  To do this:

```
$ kubectl config get-contexts

CURRENT   NAME                  CLUSTER               AUTHINFO              NAMESPACE
          kubernetes-cluster1   kubernetes-cluster1   kubernetes-cluster1
 *        kubernetes-cluster2   kubernetes-cluster2   kubernetes-cluster2
```

If the other cluster is selected then change to kubernetes-cluster2 (unlike the example above):
```
$ kubectl config use-context kubernetes-cluster2

Switched to context "kubernetes-cluster2".
```


To access the dashboard run:
```
kubectl proxy --port=8002
```

### Setup and Deploy the Sock Shop

Now we can deploy the app from the Kubernetes Dashboard. 

#### Step 1. Create Sock-shop Namespace YAML

Locally create 'sockshop-namespace.yaml' from the text below:

```
cat > sockshop-namespace.yaml << EOF
{
  "kind": "Namespace",
  "apiVersion": "v1",
  "metadata": {
    "name": "sock-shop",
    "labels": {
      "name": "sock-shop"
    }
  }
}
EOF
```

#### Step 2. Create Sock-shop Deploy YAML

To save 'sockshop-deploy.yaml' locally, run the following command:

```
curl https://raw.githubusercontent.com/microservices-demo/microservices-demo/master/deploy/kubernetes/complete-demo.yaml > sockshop-deploy.yaml
```

#### Step 3. For this application, we need to first create a namespace within the Kubernetes cluster.

1. Select [+ Create] 
2. Select tab - [Create from file]
3. Select [...] to choose YAML or JSON
4. Select the sockshop-namespace.yaml file
5. Select Upload

![Sockshop Namespace](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5a_sockshop-namespace.png)

#### Step 4. Now deploy on Kubernetes.

**The sock-shop namespace is selected by default, but ensure that you are within it first.**  Green line within screenshot below shows the selected namespace.

1. Select [+ Create] 
2. Select tab - [Create from file]
3. Select [...] to choose YAML or JSON
4. Select the sockshop-deploy.yaml file
5. Select Upload

![Sockshop Deploy](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5b_sockshop-deploy.png)

Success!

![Sockshop Success](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5c_successful-deployment.png)

### Login to Access the Sock Shop website

Use the public IP for the other DC/OS public agent 
- The first public IP used was for the DC/OS Marathon-LB
- This agent is running the public kubelet now hosting the Sock Shop website

![Public-IP](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5e_Public-IP-B.png)

http://\<DCOS PUBLIC IP\>:30001

![Sockshop Page](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5d_sockshop-pageB.png)

You have now deployed a full website in Kubernetes running on Mesosphere DC/OS.

This website includes 13 containers:
- 4 Mongo DBs (Cart database, Orders database, User databases)
- 3 Java Apps (Cart app, Order app, Shipping app)
- MySQL DB (Catalogue database)
- 4 Docker Containers (Catalogue app, Front-end app, Payment app, Queue-master app)
- RabbitMQ


