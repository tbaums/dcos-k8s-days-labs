## Create Directory Structure
This will create a templatge that we will copy and paste to create sets of clusters.
1. Create an event Directory.  (Name it whatever you wanty an navigate into it with your Terminal)
2. Create a "base" directory.  (This will serve as our Template and Dry Run Directory)
3. Go to the following github repo. https://github.com/geekbass/multi-dcos-cluster
4. Download, unzip, and move the entire newly created directory (not just its contents) to the "base" director
5. Modify the `variables.tf` as desired (Cluster Name, Node Counts, and Node Sizes)

## Initial Setup
This needs to be done first and once to set the default AWS Region and to add the key to your shell.  Execute as a single copy, paster, run step.
```
ssh-add ~/.ssh/phlk8sdcosday
export AWS_DEFAULT_REGION="us-east-1"

## For Each Cluster Set to be built

Navigate to the named cluster (Alpha, Bravo, Charlie, etc.) directory and into the multi-dcos-cluster directory.  Ensure the `variables.tf`has been modified according to the desired size and number of each node type and that the cluster name is unique to the AWS region to which you are de[ploying. 

Authenticate with AWS

Execute the following set of commands as a single copy, paste, run.
```
bash build.sh <n>
terraform init -upgrade=true
terraform plan -out plan.out
terraform apply plan.out
```

after about 15 minutes you should have a set of DC/OS Clusters.

Verify that it looks, feels, and tastes how you would like.
Once you have verified that this is what you want, go ahead and `terraform destroy`

Wash, Rinse, Repeat!
