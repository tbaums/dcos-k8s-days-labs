# Lab 3 - Configure Jenkins 

In this lab exercise, you configure your Jenkins instance to pull from GitHub, build a new Docker image with any code changes, push the Docker image to DockerHub, and deploy your application.

## Step 1 - Navigate to your Jenkins UI

From your DC/OS Services tab, hover your mouse over the Jenkins icon and click the *open in new window* icon.

![jenkins-open](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-open.png)


## Step 2 - Add DockerHub credentials

From your Jenkins UI, select *Credentials*, then select *System*. Click *Global credentials*.

In the left navigation pane, select *Add Credentials*. 

Add in your personal DockerHub credentials:
- Username: your personal DockerHub username (not email!)
- Password: your personal DockerHub password
- ID: `DockerHub`

When complete, click *OK*.

![jenkins-add-dockerhub-creds](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-add-dockerhub-creds.png)

## Step 3 - Add GitHub credentials

In the left navigation pane, select *Add Credentials*. 

Add in your personal GitHub credentials:
- Username: your personal GitHub username (not email!)
- Password: your personal GitHub password
- ID: `GitHub`

![jenkins-add-github-creds](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-add-github-creds.png)

## Step 4 - Create your Jenkins pipeline

Return to the Jenkins home screen by clicking the Jenkins logo in the top navigation bar.

![jenkins-home](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-home.png)

Next, select *Create new jobs* from the Jenkins home screen.

![jenkins-create-new](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-create-new.png)

Name your project anything you want (lowercase only, no spaces). Select *Freestyle project* and click *OK* at the bottom of the screen.

![jenkins-create-pipeline](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-create-pipeline.png)

Now, you need to point Jenkins to the GitHub repository you forked in Lab 1.

Scroll down to the **Source Code Management** section of the page, and select *Git*.

![jenkins-select-git](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-select-git.png)

In the *Repository URL* field, add the URL to your GitHub repository. You can find the URL to your repository by navigating to the repository on GitHub, selecting *Clone or Download* and copying the link, as shown.

![jenkins-find-github-url](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-find-github-url.png)

Returning to Jenikns, paste your GitHub repository URL. 

In the credentials selector, choose your GitHub credentials (which you provided to Jenkins in Step 3 above).

![jenkins-git-credentials](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-git-credentials.png)

Scroll down the page to the *Build* section.

Select *Add build step* and choose *Docker Build and Push* from the dropdown. 

Add in the following information:
- Repository name: `<your personal DockerHub username (not email!)>/cicd-demo`
- Tag: `$GIT_COMMIT`
- Registry credentials: Select the DockerHub credentials you created in Step 2 above.


![jenkins-docker-credentials](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-docker-credentials.png)

Scroll down the page once more to the **Post-build Actions** section, and select *Add post-build action*.

From within the drop-down menu, select *Marathon Deployment* and click *Advanced*.

![jenkins-post-build-click-advanced](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-post-build-click-advanced.png)


Add in the following information:
- Marathon URL: `http://marathon.mesos:8080`
- Definition file: `conf/cd-demo-app.json`
- Docker Image: `<your personal DockerHub username (not email!)>/cicd-demo:$GIT_COMMIT`

When complete, click *Save*.


![jenkins-post-build-config](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-post-build-config.png)


Congratulations! Your Jenkins build pipeline is now configured and ready to use!