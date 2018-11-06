# LAB 2 - Connecting kubectl to DC/OS

### Step 1. Deploy the DC/OS Marathon-LB (HAPROXY-based load balancer)

Run the command to install Marathon-LB. This will allow us to expose the Kubernetes API Server port (in step 2):

```
dcos package install marathon-lb --yes
```

Once completed, you should see `Marathon-LB` running and healthy in the Services tab in your DC/OS cluster.

![marathon-lb](https://github.com/tbaums/rccl-k8s/blob/master/screenshots/marathon-lb.png)


### Step 2. Launch a proxy service on DC/OS

Next, you need to launch a proxy service on DC/OS to expose the Kubernetes API Server port. 

### Step 2.a

Create a kubectl-proxy service specification file:

**Be sure to copy and paste the entire code snippet below (including the final line containing `EOF`)**
```
cat <<EOF > cluster1-kubectl-proxy.json
{
  "id": "/cluster1-kubectl-proxy",
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
    "HAPROXY_0_PORT": "6443",
    "HAPROXY_0_BACKEND_SERVER_OPTIONS": "  timeout connect 10s\n  timeout client 86400s\n  timeout server 86400s\n  timeout tunnel 86400s\n  server kubernetescluster1 apiserver.kubernetes-cluster1.l4lb.thisdcos.directory:6443 ssl verify none\n"
  }
}
EOF
```

### Step 2.b 

Deploy the cluster1-kubectl-proxy service with the command:
```
dcos marathon app add cluster1-kubectl-proxy.json
```

Here is how this works:
* Marathon-LB identifies that the application kubeapi-proxy has the HAPROXY_GROUP label set to external (change this if you're using a different HAPROXY_GROUP for your Marathon-LB configuration).
* The instances, cpus, mem, cmd, and container fields basically create a dummy container that takes up minimal space and performs no operation.
* The single port indicates that this application has one "port" (this information is used by Marathon-LB)
* "HAPROXY_0_MODE": "http" indicates to Marathon-LB that the frontend and backend configuration for this particular service should be configured with http.
* "HAPROXY_0_PORT": "6443" tells Marathon-LB to expose the service on port 6443 (rather than the randomly-generated service port, which is ignored)
* "HAPROXY_0_SSL_CERT": "/etc/ssl/cert.pem" tells Marathon-LB to expose the service with the self-signed Marathon-LB certificate (which has no CN)
* The last label HAPROXY_0_BACKEND_SERVER_OPTIONS indicates that Marathon-LB should forward traffic to the endpoint apiserver.kubernetes-cluster1.l4lb.thisdcos.directory:6443 rather than to the dummy application, and that the connection should be made using TLS without verification.


### Step 3. Find public IP address of Public DC/OS Node

To attach kubectl to your cluster, you must point the configuration command to the public IP address of the public DC/OS agent where Marathon-LB is running. 

Your DC/OS cluster contains 2 public agent nodes. By default, Marathon-LB will deploy only to a public agent. To determine where Marathon-LB is running, open your `clusterinfo.txt` file and note the IP addresses of your public agents.

Next, attempt to visit the following URL *for each of your public agents*:

```
<public agent public IP address>:9090/haproxy?stats
```

Whichever public agent node shows the Marathon-LB stats page is the node where Marathon-LB is running (see below).

![marathon-lb-stats](https://github.com/tbaums/rccl-k8s/blob/master/screenshots/marathon-lb%20stats.png)


### Step 4. Connecting using Kubeconfig

### Step 4.a 

Configure kubectl to connect to the Kubernetes cluster running on  DC/OS using the following commands:
```
dcos kubernetes cluster kubeconfig \
    --insecure-skip-tls-verify \
    --context-name=kubernetes-cluster1 \
    --cluster-name=kubernetes-cluster1 \
    --apiserver-url=https://${MARATHON_PUB_IP}:6443
```

### Step 4.b

Confirm connection:

```
kubectl get nodes
```

### Step 5. Kubernetes Dashboard (Official UI of Kubernetes)

(NOTE: if you are using a bootstrap server to access your cluster then the local proxy will not give you access to the Dashboard.)

To access the dashboard run:

```
kubectl proxy
```

Point your browser to:

```
http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/
```

