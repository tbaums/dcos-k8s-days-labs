### Lab 3 - Making Kubernetes Highly Available through the GUI

You can choose to make Kubernetes on DC/OS high availability (HA) at the time of deployment. However, you can also make non-HA clusters into HA by editing and saving the configuration. 

In the DC/OS UI under Services, click on Kubernetes Cluster 1 (Not kube-proxy). 

![](https://github.com/tbaums/rccl-k8s/blob/master/screenshots/k8s-ha-1.png)

In the top right, click Edit.

![](https://i.imgur.com/2dYmVLp.png)

Under Kubernetes in left hand menu, choose the high availability box

![](https://github.com/tbaums/rccl-k8s/blob/master/screenshots/select-ha.png)

Save configuration and watch new components come online with the following command or in the GUI. Output should look like below:

```
$ dcos kubernetes manager plan status deploy --name=kubernetes-cluster1

deploy (serial strategy) (COMPLETE)
├─ etcd (serial strategy) (COMPLETE)
│  ├─ etcd-0:[peer] (COMPLETE)
│  ├─ etcd-1:[peer] (COMPLETE)
│  └─ etcd-2:[peer] (COMPLETE)
├─ control-plane (serial strategy) (IN_PROGRESS)
│  ├─ kube-control-plane-0:[instance] (COMPLETE)
│  ├─ kube-control-plane-1:[instance] (COMPLETE)
│  └─ kube-control-plane-2:[instance] (COMPLETE)
├─ mandatory-addons (serial strategy) (COMPLETE)
│  └─ mandatory-addons-0:[instance] (COMPLETE)
├─ node (parallel strategy) (COMPLETE)
│  └─ kube-node-0:[kubelet] (COMPLETE)
└─ public-node (serial strategy) (COMPLETE)

```

**Notice that in high availability mode, DC/OS has started 3 etcd peers and 3 kube-control-plane processes. If any of these processes die, DC/OS will monitor their health and automatically restart them.**



