# Bootstrap Node Prep Step-By-Step

these instructions are to prepare the Boodstrap node of a cluster for a DC/OS Day.  It will do the following:
* Install Shell-In-A-Box: browser-based Linux terminal session
* Install KUBECTL: command line utility for Kubernetes
* Begin the process for setting the "centos" user password.

## Step-By-Step Instructions

Begin by logging into the Bootstrap node using the appropriate ssh key.
```
ssh -i "<path-to-key>" centos@<bootstrap-public-ip>
```

Copy and paste the below block of text in a single step.  It will complete by asking you to set the centos user password.
```
sudo su
wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -ivh epel-release-latest-7.noarch.rpm
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
yum install -y kubectl openssl shellinabox
systemctl enable shellinaboxd.service
systemctl start shellinaboxd.service
passwd centos
```

## Verify It Worked

Log into browser CLI session 

```
https://<bootstrap-public-ip>:4200
userid: centos
password: <new-password>
```
Run `kubectl` from the command line to make sure it is installed properly.

## Upload to Repo

Make sure to upload the SSH key and the newly created password to the appropriate sections of this repository.



