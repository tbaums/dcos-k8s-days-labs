# Lab 5 - Deploy an App from Image Repo

### Use the Kubernetes-cluster2 K8s Dashboard

Ensure that you have connectivity to Kubernetes-cluster2 by pointing your browser to:

http://127.0.0.1:8002/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

If the above URL does not work, then the proxy needs to be restarted.  We need to ensure that we have selected the kubernetes-cluster2.  To do this:

```
$ kubectl config get-contexts

CURRENT   NAME        CLUSTER     AUTHINFO    NAMESPACE
          kcluster1   kcluster1   kcluster1
*         kcluster2   kcluster2   kcluster2
```

If the other cluster is selected then change to kcluster2 (unlike the example above):
```
$ kubectl config use-context kcluster2

Switched to context "kcluster2".
```

To access the dashboard run:
```
kubectl proxy --port=8002
```

### Setup and Deploy the Sock-Shop

It is possible to create an app from the Kubernetes Dashboard. 
#### Step 1. Create Sockshop Namespace YAML

Create 'sockshop-namespace.yaml' from the text below:

```
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
```

#### Step 2. Create Sockshop Deploy YAML

Copy from https://github.com/microservices-demo/microservices-demo/blob/master/deploy/kubernetes/complete-demo.yaml
and save locally as 'sockshop-deploy.yaml'

#### Step 3. For this application, we need to first create a namespace within the Kubernetes cluster.

1. Select [+ Create] 
2. Select tab - [Create from file]
3. Select [...] to choose YAML or JSON
4. Select the lab5a_sockshop-namespace.yaml file
5. Select Upload

![Sockshop Namespace](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5a_sockshop-namespace.png)

#### Step 4. Now deploy on Kubernetes.

The sock-shop namespace is selected by default, but ensure that you are within it first.  Green line within screenshot below shows the selected namespace.

1. Select [+ Create] 
2. Select tab - [Create from file]
3. Select [...] to choose YAML or JSON
4. Select the lab5a_sockshop-deploy.yaml file
5. Select Upload

![Sockshop Deploy](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5b_sockshop-deploy.png)

Success!

![Sockshop Success](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5c_successful-deployment.png)

### Login to the Sock Shop website

Find the public IP for the DC/OS agent running the public kubelet...
(It will not be the same IP as the Marathon-LB)

http://\<DCOS PUBLIC IP\>:30001

![Sockshop Page](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5d_sockshop-page.png)
