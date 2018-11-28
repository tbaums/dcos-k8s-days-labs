# Lab 1 - Environment Setup

In this series of labs, you will learn how to deploy a full CICD pipeline on DC/OS using Jenkins. 

By the end of this lab series, you will have configured Jenkins to: 

1. monitor a GitHub repository for new commits, 
1. pull the code from GitHub when Jenkins discovers new commits, 
1. build the new code into a Docker container, 
1. push the new container to DockerHub, and
1. deploy the new application!

## Prerequisites

In order to complete these lab exercises, please ensure you have the following:

1. GitHub account and access to your credentials 
1. DockerHub account and access to your credentials
1. DC/OS cluster

## Step 1 - Install Jenkins on DC/OS

Within your DC/OS UI, select *Catalog* in the left navigation pane.

![jenkins-select-catalog](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-select-catalog.png)

Select Jenkins from the list of certified packages.

![jenkins-select](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-select.png)

Select *Review & Run*.

![jenkins-review-and-run](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-review-and-run.png)

You do not need to change the default configuration settings. Click *Review & Run* again.

![jenkins-review-and-run2](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-review-and-run2.png)

Lastly, click *Run Service* to initiate your Jenkins deployment.

![jenkins-run-service](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-run-service.png)

You can monitor the Jenkins launch process in your DC/OS Services tab.

## Step 2 - Install Marathon-LB

**Note: If you already have Marathon-LB running in your cluster, please skip this step entirely.**

Marathon-LB is an easy-to-use, HAProxy-based load balancer most commonly used to expose services running on DC/OS to the public Internet.

To install Marathon-LB on DC/OS, first navigate to the *Catalog* tab in your DC/OS UI.

![catalog-tab](https://github.com/tbaums/dcos-days-prometheus-grafana-labs/blob/master/screenshots/catalog-tab.png)


In the search box, search for `marathon-lb` and select *marathon-lb*.

![marathon-search](https://github.com/tbaums/dcos-days-prometheus-grafana-labs/blob/master/screenshots/marathon-search.png)

Next, click *Review & Run* to open the configuration settings for your new Marathon-LB instance.

![marathon-review-and-run](https://github.com/tbaums/dcos-days-prometheus-grafana-labs/blob/master/screenshots/marathon-review-and-run.png)

Without making any changes to the configuration, click *Review and Run* again.

![marathon-review-and-run-2](https://github.com/tbaums/dcos-days-prometheus-grafana-labs/blob/master/screenshots/marathon-review-and-run-2.png)

As a last step, click *Run Service*.

![marathon-run-service](https://github.com/tbaums/dcos-days-prometheus-grafana-labs/blob/master/screenshots/marathon-run-service.png)

In the *Services* tab of the DC/OS GUI, you should now see Marathon-LB starting up.

![marathon-booting-up](https://github.com/tbaums/dcos-days-prometheus-grafana-labs/blob/master/screenshots/marathon-booting-up.png)







## Step X - Clone the `cd-demo` repository



Please navigate to https://github.com/mesosphere/cd-demo. 

Clone CICD Git Repo - (Dan to provide external facing link)

Deploy Jenkins in DC/OS (via the UI/Catalog is the easiest)