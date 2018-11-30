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
#### Step 1. Download the 2 Sock-shop YAML files

Give download location...

#### Step 2. For this application, we need to first create a namespace within the Kubernetes cluster.

Select [+ Create]; Select tab - [Create from file]; Select [...] to choose YAML or JSON; Select the lab5a_sockshop-namespace.yaml file; then Upload.

![Sockshop Namespace](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5a_sockshop-namespace.png)

#### Step 3. Now deploy on Kubernetes.

The sock-shop namespace is selected by default, but ensure that you are within it first.  Green line within screenshot below shows the selected namespace.

Select [+ Create]; Select tab - [Create from file]; Select [...] to choose YAML or JSON; Select the lab5a_sockshop-deploy.yaml file; then Upload.

![Sockshop Deploy](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5a_sockshop-deploy.png)


Can use the hello-server image in Google Repo

```
gcr.io/google-samples/hello-app:1.0
```
