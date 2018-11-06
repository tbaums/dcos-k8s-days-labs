# Launch a second Kubernetes cluster

In this lab, you will launch a second Kubernetes cluster on the same DC/OS cluster, demonstrating Mesosphere Kubernetes Engine's ability to run high-density, multi-kubernetes environments.

### Step 1. Create SSL keys, Service Account, and Secret for Kubernetes Cluster 2

Run the commands below to create the SSL keys, service account, and secret for your second Kubernetes cluster.

```
dcos security org service-accounts keypair private-key.pem public-key.pem
dcos security org service-accounts create -p public-key.pem -d 'Kubernetes cluster 2 service account' kubernetes-cluster2
dcos security secrets create-sa-secret private-key.pem kubernetes-cluster2 kubernetes-cluster2/sa
```

### Step 2. Add permissions

Run the commands below to grant the service account permissions to create and view Kubernetes clusters.

```
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:framework:role:kubernetes-cluster2-role create
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:task:user:root create
dcos security org users grant kubernetes-cluster2  dcos:mesos:agent:task:user:root create

dcos security org users grant kubernetes-cluster2  dcos:mesos:master:reservation:role:kubernetes-cluster2-role create
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:reservation:principal:kubernetes-cluster2 delete
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:volume:role:kubernetes-cluster2-role create
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:volume:principal:kubernetes-cluster2 delete

dcos security org users grant kubernetes-cluster2  dcos:secrets:default:/kubernetes-cluster2/* full
dcos security org users grant kubernetes-cluster2  dcos:secrets:list:default:/kubernetes-cluster2 read

dcos security org users grant kubernetes-cluster2  dcos:adminrouter:ops:ca:rw full
dcos security org users grant kubernetes-cluster2  dcos:adminrouter:ops:ca:ro full

dcos security org users grant kubernetes-cluster2  dcos:mesos:master:framework:role:slave_public/kubernetes-cluster2-role create
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:framework:role:slave_public/kubernetes-cluster2-role read
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:reservation:role:slave_public/kubernetes-cluster2-role create
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:volume:role:slave_public/kubernetes-cluster2-role create
dcos security org users grant kubernetes-cluster2  dcos:mesos:master:framework:role:slave_public read
dcos security org users grant kubernetes-cluster2  dcos:mesos:agent:framework:role:slave_public read
```

### Step 3. Launch the second Kubernetes cluster

Now, we will launch a Kubernetes cluster using the service account and secret we just created. Copy and paste the command below into your terminal to create a package installer options file that references the service account and secret we just created.

**Be sure to copy and paste the entire code snippet below (including the final line containing `EOF`)**

```
cat > cluster2-options.json << EOF
{
  "service": {
    "name": "kubernetes-cluster2",
    "service_account": "kubernetes-cluster2",
    "service_account_secret": "kubernetes-cluster2/sa"
  }
}
EOF
```

Then run the DC/OS Kubernetes CLI command to launch the Kubernetes cluster.

```
dcos kubernetes cluster create --options=cluster2-options.json --yes
```

Your new Kubernetes cluster will take a few minutes to spin up. You can see the installation runbook automation and monitor the status of installation of each component with the command below:

```
dcos kubernetes manager plan status deploy --name=kubernetes-cluster2
```

When all the Kubernetes cluster elements report a status of `COMPLETE`, your new cluster is ready to use.

### Step 4. Configure Marathon-LB to expose Kubernetes API

Run the commands below:

**Be sure to copy and paste the entire code snippet below (including the final line containing EOF)**

Note, we set Marathon-LB to expose kubernetes-cluster2's API on port 6444 (so as not to conflict with kubernetes-cluster1).

```
cat <<EOF > cluster2-kubectl-proxy.json
{
  "id": "/cluster2-kubectl-proxy",
  "instances": 1,
  "cpus": 0.001,
  "mem": 16,
  "cmd": "tail -F /dev/null",
  "container": {
    "type": "MESOS"
  },
  "portDefinitions": [
    {
      "protocol": "tcp",
      "port": 0
    }
  ],
  "labels": {
    "HAPROXY_0_MODE": "http",
    "HAPROXY_GROUP": "external",
    "HAPROXY_0_SSL_CERT": "/etc/ssl/cert.pem",
    "HAPROXY_0_PORT": "6444",
    "HAPROXY_0_BACKEND_SERVER_OPTIONS": "  timeout connect 10s\n  timeout client 86400s\n  timeout server 86400s\n  timeout tunnel 86400s\n  server kubernetescluster2 apiserver.kubernetes-cluster2.l4lb.thisdcos.directory:6443 ssl verify none\n"
  }
}
EOF
```

Next, deploy the proxy with the command below:

```
dcos marathon app add cluster2-kubectl-proxy.json
```

### Step 5. Configure kubectl for the second Kubernetes cluster

Configure kubectl to connect to the Kubernetes cluster running on  DC/OS using the following commands:

```
dcos kubernetes cluster kubeconfig \
    --insecure-skip-tls-verify \
    --context-name=kubernetes-cluster2 \
    --cluster-name=kubernetes-cluster2 \
    --apiserver-url=https://<public agent where Marathon-LB is running IP address>:6444
```

### Step 6. Expose the second Kubernetes dashboard

(NOTE: if you are using a bootstrap server to access your cluster then the local proxy will not give you access to the Dashboard.)

To access the dashboard run:
```
kubectl proxy --port=8002
```
Point your browser to:

http://127.0.0.1:8002/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
