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

## From the CLI

Export the current package configuration into a JSON file called config.json

```
INPUT COMMAND
```

