# Lab 4 - Run your Jenkins Build!

With your fully configured Jenkins instance ready to pull from GitHub, push to DockerHub, and deploy your Dockerized application, it's time for a test drive!

## Step 1 - Kick off Jenkins job

From the Jenkins home screen, click *Build Now* from the left navigation pane.

![jenkins-build-now](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-build-now.png)

## Step 2 - Observe your build process

You can observe the progress of your Jenkins pipeline by clicking the build and selecting *Console output*.

You can also observe DC/OS spinning up a new Jenkins executor to execute the pull/build/push process from the Jenkins service page within your DC/OS UI. 

![jenkins-observe-progress](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-observe-progress.png)

 ## Step 3 - Visit your new Jekyll site!

 Point your browser to your DC/OS public node running Marathon-LB (public IP, no port needed).

 You should see your Jekyll site as below!

 ![jenkins-jekyll-site](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/jenkins-jekyll-site.png)
