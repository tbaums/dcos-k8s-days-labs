# Lab 2 - Configure GitHub Repository 

In this lab exercise, you will modify your forked `cd-demo` repository to point to your DockerHub account and your DC/OS cluster.

## Step 1 - Navigate to `/conf/cd-demo-app.json`

From your forked `cd-demo` repository homepage, navigate to the `conf` directory.

![github-conf](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/github-conf.png)


Within the `conf` directory, select `cd-demo-app.json`. This file contains all the necessary specifications for Marathon to deploy your app and expose it publicly via Marathon-LB (installed in Lab 1).

![github-conf2](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/github-conf2.png)


Next, select the pencil icon to make changes to the `cd-demo-app.json` file directly in your browser.

![github-edit](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/github-edit.png)

You need to adjust two values in this JSON specification. 

1. Modify the Docker image to `<your Docker hub username>/cd-demo-app:latest`. This is where Marathon will look to pull the app's container image after Jenkins has pushed it to Dockerhub. Please note that while DockerHub allows you to log in with your email address, *you must use your DockerHub **username** here*.
1. Change the `HAPROXY_0_VHOST` value to the **public IP of the DC/OS public agent where Marathon-LB is running**, *without* leading `http://`. To confirm that Marathon-LB is running, simply point your browser to `<your public agent public IP>:9090/haproxy?stats`. If your DC/OS cluster has more than one public agent, attempt the above with each of your public agent public IP addresses. Whichever public agent returns the HAProxy stats page is the public agent where Marathon-LB is running. Use this public agent's IP as the value for the `HAPROXY_0_VHOST` parameter.

![github-edit2](https://github.com/tbaums/dcos-k8s-days-labs/blob/master/labs/CICD-labs/screenshots/github-edit2.png)

