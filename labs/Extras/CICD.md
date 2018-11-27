## Lab CICD - Prerequisites

GitHub Account (w/credentials)

DockerHub Account (w/credentials)

Clone CICD Git Repo - (Dan to provide external facing link)

Deploy Jenkins in DC/OS (via the UI/Catalog is the easiest)

## Edit Your JSON file

Modify the “conf/cd-demo-app.js” in the GitHub Repository that you forked for the demo.

The simplest way to do it is to navigate in the browser into the file, click the pencil (upper right) to edit, and the make the appropriate changes.  Don’t forget to “Commit Changes” (bottom) when you are done.
This can be done either before or during the demo based on desire, time, and confidence level.


![](https://i.imgur.com/R4iyLmx.png)

## Add Credentials to Jenkins

You need to add credentials to both your GitHub and DockerHub accounts:
```
Credentials-->System     
              -->Global Credentials (Unrestricted)          
                  -->Add Credentials   
```

This section describes how to input credentials for GitHub and Docker Hub.  this assumes that you have forked mesosphere/cd-demo

You will need to add two sets of credentials.

GitHub and DockerHub

![](https://i.imgur.com/ZBBxiVw.png)

## Create a new Freeform Project

Opening the Jenkins Web UI and click the “create new jobs” link under the “Welcome to Jenkins” banner
Select “Freestyle project” and enter a name for your project (lover case letters and “-” only please)
Click “OK”

![](https://i.imgur.com/snQOEAk.png)

## Configure Source Code Management

In the configuration screen, navigate to the “Source Code Management” section, and configure the following entries
```
Repository URL: Enter the URL for your forked GitHub repo
Credentials: Select your previously configured GitHub Credentials
Click OK/APPLY
```
![](https://i.imgur.com/MRtjjH2.png)

## Configure the Build

Navigate to the “Build” section of the configuration page
```
-->Add Build Step
  -->Docker Build and Publish
```
Configure the following Entries for Docker Build and Publish:
```
Repository Name: Enter your Docker Hub repo name.  If it does not yet exist, it will be created during the deployment
Tag“$GIT_COMMIT” tells Jenkins to append the build ID number to the Image as its tag
Registry Credentials: Select your previously created DockerHub Credentials
If desired, you can select “Poll SCM” and enter   “*/5 * * * *” to poll GitHub for changes every 5 minutes.
```
![](https://i.imgur.com/cRrSE4Z.png)

## Add Post Build Actions

Navigate to the “Post-build Actions” section, select the “Add post-build action”, select “Marathon Deployment” and configure the following:
```
Marathon URLEnter “http:///leader.mesos:8080”
Definition File: Enter the path to the App-Deployment file in the GitHub Repo.  Default is “conf/cd-demo-app.json”
Under Advanced
Docker Image: Specifies the image to pull from your DockerHub Repo
```
Click !!!APPLY!!! ---> Click !!!SAVE!!!

![](https://i.imgur.com/RSK1HJB.png)

## Add Post Build Actions

