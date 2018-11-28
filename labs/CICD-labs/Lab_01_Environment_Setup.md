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

![jenkins-select-catalog]()

Select Jenkins from the list of certified packages.

![jenkins-select]()

Select *Review & Run*.

![jenkins-review-and-run]()

You do not need to change the default configuration settings. Click *Review & Run* again.

![jenkins-review-and-run]()

Lastly, click *Run Service* to initiate your Jenkins deployment.

![jenkins-run-service]()








## Step X - Clone the `cd-demo` repository



Please navigate to https://github.com/mesosphere/cd-demo. 

Clone CICD Git Repo - (Dan to provide external facing link)

Deploy Jenkins in DC/OS (via the UI/Catalog is the easiest)