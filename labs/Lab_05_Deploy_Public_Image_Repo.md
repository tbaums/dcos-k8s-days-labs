## Lab 5 - Deploy an App from Image Repo

## Use Kubernetes-cluster2

Kubectl can only control one Kubernetes cluster at a time.  We need to ensure that we have selected the kubernetes-cluster1.  To do this:

```
$ kubectl config get-contexts

CURRENT   NAME        CLUSTER     AUTHINFO    NAMESPACE
*         kcluster1   kcluster1   kcluster1
          kcluster2   kcluster2   kcluster2
```

If the other cluster is selected then change to kcluster1 (unlike the example above):
```
$ kubectl config use-context kcluster1

Switched to context "kcluster1".
```
It is possible to create an app from the Kubernetes Dashboard. For this application, we need to create a namespace within the Kubernetes cluster.

![Sockshop Namespace](https://github.com/jdyver/dcos-k8s-days-labs/blob/master/screenshots/lab5a_sockshop-namespace.png)

Can use the hello-server image in Google Repo

```
gcr.io/google-samples/hello-app:1.0
```
