# Lab 1 Launch a Kubernetes  Cluster

The instructor will give you the IP address and credentials that you will need to SSH into your DC/OS cluster.

### Step 1. Set Up DC/OS Command Line

### Step 1.a

If you have a Macbook or Linux laptop and you don't have any restrictions on accessing servers on the internet, you can use the instructions in Step 1.b on your laptop. If you don't then you should login to your cluster's "bootstrap" server and use it as a command line client.

Download the id_rsa key from the workshop cluster Github location at:

https://github.com/tbaums/dcos-k8s-days-labs/tree/master/keys

If using Windows and Putty Telnet, you will need to download the key above and convert it to a `.ppk` file. Please see the instructor with questions.  




### Step 1.b

If your laptop can access any servers on the internet without restriction, please install the DC/OS CLI locally on your machine. If your laptop is restricted, please SSH into your "bootstrap" server and install the DC/OS CLI there. For the rest of the course, you will execute DC/OS command line commands from your bootstrap server (located within your VPC).

To SSH to the bootstrap server:
```
ssh -i ./id_rsa centos@<your bootstrap server ip address>
```

**All students continue here:**


Set up the DC/OS command line by clicking on the top left and choosing "Install CLI"

![CLI](https://github.com/tbaums/rccl-k8s/blob/master/screenshots/dcos-cli-1.png)

Click in the dialogue box to copy the command

![Copy Command](https://i.imgur.com/3rQ2Unj.png)

Paste that command into your Terminal and press enter

For CoreOS use the following commands to install the CLI binary:

```
sudo mkdir -p /opt/bin && 
curl https://downloads.dcos.io/binaries/cli/linux/x86-64/dcos-1.12/dcos -o dcos && 
chmod +x dcos &&
sudo mv dcos /opt/bin

```

**Note: DC/OS CLI instructions default to `http`. For the purposes of these lab exercises, however, we must set up and attach to our DC/OS clusters using `https`. After installing the CLI, please follow the steps below to detach and reattach to your cluster using `https`:**

```
dcos cluster remove --all
```
```
dcos cluster setup https://<master node IP address>
```

Once the CLI is installed, confirm that it is installed correctly and connected to your cluster by running following command

```
dcos node
```

The output should be a list of nodes in the cluster

```

   HOSTNAME        IP                         ID                     TYPE                 REGION          ZONE       
  10.0.0.101   10.0.0.101  94141db5-28df-4194-a1f2-4378214838a7-S0   agent            aws/us-west-2  aws/us-west-2a  
  10.0.2.100   10.0.2.100  94141db5-28df-4194-a1f2-4378214838a7-S4   agent            aws/us-west-2  aws/us-west-2a
```

### Step 2. Tour DC/OS Catalog

Your instructor will give you a tour of DC/OS UI and catalog. 

### Step 3. Launch a Kubernetes Cluster 

To launch a Kubernetes cluster, you must first deploy the Mesosphere Kubernetes Control Plane Manager. 

### Step 3.a 

Install the Kubernetes Control Plane Manager with the following command:

```
dcos package install kubernetes --yes
```

```
By Deploying, you agree to the Terms and Conditions https://mesosphere.com/catalog-terms-conditions/#certified-services
Mesosphere Kubernetes Engine
Installing Marathon app for package [kubernetes] version [2.0.0-1.12.1]
Installing CLI subcommand for package [kubernetes] version [2.0.0-1.12.1]
New command available: dcos kubernetes
The Mesosphere Kubernetes Engine service is being installed.
```

You can check to see if the control manager is installed completely by running the following command:

```
dcos kubernetes manager plan status deploy
```

```
deploy (serial strategy) (COMPLETE)
└─ mesosphere-kubernetes-engine (serial strategy) (COMPLETE)
   └─ mesosphere-kubernetes-engine-0:[cosmos-adapter] (COMPLETE)
```

When all steps are "COMPLETE", confirm that the "dcos kubernetes" CLI was installed.

```
dcos kubernetes --help
```

### Step 3.b 

Once the Kubernetes control plan manager is running, you can use it to launch a Kubernetes cluster.  Since you are using the Enterprise version of DC/OS, you can use the DC/OS certificate authoritity to create an SSL key to be used with a DC/OS service account user.

First, you must install the DC/OS Enterprise CLI, which gives you access to DC/OS Enterprise security features, among other useful tools.

```
dcos package install dcos-enterprise-cli --yes
```

Next, run the following commands to create the SSL keys, the service account and the secret.

```
dcos security org service-accounts keypair private-key.pem public-key.pem
dcos security org service-accounts create -p public-key.pem -d "Kubernetes cluster 1 service account" kubernetes-cluster1
dcos security secrets create-sa-secret private-key.pem kubernetes-cluster1 kubernetes-cluster1/sa
```

Once completed, you should be able to see the secret by clicking on the `Secrets` tab in the left navigation pane.

![secrets](https://github.com/tbaums/rccl-k8s/blob/master/screenshots/dcos-view-secrets.png)

You should also grant the correct permissions to allow the new service account to launch and view Kubernetes clusters. Run the following ACL commands:

```
dcos security org groups add_user superusers kubernetes-cluster1
```
*Note: See below for an example of more granular security settings that more closely resembles what you would use in production ([link](#production-permissions)).*

Now, we will launch a Kubernetes cluster using the service account and secret we just created. Copy and paste the command below into your terminal to create a package installer options file that references the service account and secret we just created.

**Be sure to copy and paste the entire code snippet below (including the final line containing `EOF`)**

```
cat > cluster1-options.json << EOF
{
  "service": {
    "name": "kubernetes-cluster1",
    "service_account": "kubernetes-cluster1",
    "service_account_secret": "kubernetes-cluster1/sa"
  }
}
EOF
```

Then run the DC/OS Kubernetes CLI command to launch the Kubernetes cluster.

```
dcos kubernetes cluster create --options=cluster1-options.json --yes
```

```
By Deploying, you agree to the Terms and Conditions https://mesosphere.com/catalog-terms-conditions/#certified-services
Kubernetes on DC/OS.

	Documentation: https://docs.mesosphere.com/service-docs/kubernetes-cluster
	Issues: https://github.com/mesosphere/dcos-kubernetes-quickstart/issues

Creating Kubernetes cluster kubernetes-cluster1
DC/OS Kubernetes is being installed!
Kubernetes cluster '[kubernetes-cluster1]' is being created
```

Your new Kubernetes cluster will take a few minutes to spin up. You can see the installation runbook automation and monitor the status of installation of each component with the command below:

```
dcos kubernetes manager plan status deploy --name=kubernetes-cluster1
```
First, it will show some Kubernetes components completed, and some started or pending like this:

```
$ dcos kubernetes manager plan status deploy --name=kubernetes-cluster1

deploy (serial strategy) (IN_PROGRESS)
├─ etcd (serial strategy) (COMPLETE)
│  └─ etcd-0:[peer] (COMPLETE)
├─ control-plane (dependency strategy) (STARTED)
│  └─ kube-control-plane-0:[instance] (STARTED)
├─ mandatory-addons (serial strategy) (PENDING)
│  └─ mandatory-addons-0:[instance] (PENDING)
├─ node (dependency strategy) (PENDING)
│  └─ kube-node-0:[kubelet] (PENDING)
└─ public-node (dependency strategy) (COMPLETE)
```

When it is completely installed, the plan status should look like this:

```
$ dcos kubernetes manager plan status deploy --name=kubernetes-cluster1

deploy (serial strategy) (COMPLETE)
├─ etcd (serial strategy) (COMPLETE)
│  └─ etcd-0:[peer] (COMPLETE)
├─ control-plane (dependency strategy) (COMPLETE)
│  └─ kube-control-plane-0:[instance] (COMPLETE)
├─ mandatory-addons (serial strategy) (COMPLETE)
│  └─ mandatory-addons-0:[instance] (COMPLETE)
├─ node (dependency strategy) (COMPLETE)
│  └─ kube-node-0:[kubelet] (COMPLETE)
└─ public-node (dependency strategy) (COMPLETE)

```

### Step 4. Install Kubernetes kubectl Command Line

**For Windows** with `curl` installed the commands are:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/windows/amd64/kubectl.exe
```

Then, add the binary to your PATH.

**For Macs** with `brew` installed the command is:

```
brew install kubectl
```

**For CoreOS** the commands are:
```
curl -O https://storage.googleapis.com/kubernetes-release/release/v1.12.1/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mkdir -p /opt/bin
sudo mv kubectl /opt/bin/kubectl
```

**For Red Red or CentOS** the commands are:
```
curl -O https://storage.googleapis.com/kubernetes-release/release/v1.12.1/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mkdir -p /usr/local/bin
sudo mv kubectl /usr/local/bin/kubectl
```

**For Ubuntu** the commands are:

```
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo touch /etc/apt/sources.list.d/kubernetes.list 
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

If your OS is not listed above, or if you need additional help installing kubectl please see [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

Confirm that kubectl is installed and in path `/usr/local/bin` using the command below. (Kubectl will say it is not connected to a dcos cluster yet, which is expected.)

```
kubectl version
```


-----------


### Production Permissions

Below are a set of example permissions for a Kubernetes cluster on DC/OS that more closely resemble what would be used in a production environment. They are provided for illustrative purposes only, and are not required for this lab exercise.

```
dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:task:user:root create
dcos security org users grant kubernetes-cluster dcos:mesos:agent:task:user:root create

dcos security org users grant kubernetes-cluster dcos:mesos:master:reservation:role:kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:reservation:principal:kubernetes-cluster delete
dcos security org users grant kubernetes-cluster dcos:mesos:master:volume:role:kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:volume:principal:kubernetes-cluster delete

dcos security org users grant kubernetes-cluster dcos:secrets:default:/kubernetes-cluster/* full
dcos security org users grant kubernetes-cluster dcos:secrets:list:default:/kubernetes-cluster read

dcos security org users grant kubernetes-cluster dcos:adminrouter:ops:ca:rw full
dcos security org users grant kubernetes-cluster dcos:adminrouter:ops:ca:ro full

dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:slave_public/kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:slave_public/kubernetes-cluster-role read
dcos security org users grant kubernetes-cluster dcos:mesos:master:reservation:role:slave_public/kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:volume:role:slave_public/kubernetes-cluster-role create
dcos security org users grant kubernetes-cluster dcos:mesos:master:framework:role:slave_public read
dcos security org users grant kubernetes-cluster dcos:mesos:agent:framework:role:slave_public read
```


