# Launch Jenkins
  - Launch Jenkins as a Docker Container with the following command:
  - docker run -d -u root --name jenkins \
      -p 8080:8080 -p 50000:50000 \
      -v /root/jenkins_2112:/var/jenkins_home \
      jenkins/jenkins:2.112-alpine

  -All plugins and configurations get persisted to the host (ssh root@host01) at _/root/jenkins2112. Port 8080 opens the web dashboard, 50000 is used to communicate with other Jenkins agents. Finally, the image has an alpine base to reduce the size footprint.


# Load Dashboard
  - You can load the Jenkins' dashboard via the following URL https://2886795344-8080-ollie02.environments.katacoda.com/
  - The username is admin with the password the default 344827fbdbfb40d5aac067c7a07b9230
  - On your own system, the password can be found via docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
  - It may take a couple of seconds for Jenkins to finish starting and be available. In the next steps, you'll use the dashboard to configure the plugins and start building Docker Images.
  
# Configure Docker Plugin
  - The first step is to configure the Docker plugin. The plugin is based on a Jenkins Cloud plugin. When a build requires Docker, it will create a "Cloud Agent" via the plugin. The agent will be a Docker Container configured to talk to our Docker Daemon.

  - The Jenkins build job will use this container to execute the build and create the image before being stopped. The Docker Image will be stored on the configured Docker Daemon. The Image can then be pushed to a Docker Registry ready for deployment.

  - Task: Install Plugin
    - Within the Dashboard, select Manage Jenkins on the left.
    - On the Configuration page, select Manage Plugins.
    - Manage Plugins page will give you a tabbed interface. Click Available to view all the Jenkins plugins that can be installed.
    - Using the search box, search for Docker. There are multiple Docker plugins, select Docker using the checkbox under the Cloud Providers header.
    - Your Jenkins server can now be configured to build Docker Images.
    
# Add Docker Agent
  - Once the plugins have been installed, you can configure how they launch the Docker Containers. The configuration will tell the plugin which Docker Image to use for the agent and which Docker daemon to run the containers and builds on.

  - The plugin treats Docker as a cloud provider, spinning up containers as and when the build requires them.

  - Task: Configure Plugin
    - This step configures the plugin to communicate with a Docker host/daemon.
    - Once again, select Manage Jenkins.
    - Select Configure System to access the main Jenkins settings.
    - At the bottom, there is a dropdown called Add a new cloud. Select Docker from the list.
    - The Docker Host URI is where Jenkins launches the agent container. In this case, we'll use the same daemon as running Jenkins, but you could split the two for scaling. Enter the URL tcp://172.17.0.80:2345
    - Use Test Connection to verify Jenkins can talk to the Docker Daemon. You should see the Docker version number returned.
The Host IP address is the IP of your build agent / Docker Host.

  - Task: Configure Docker Agent Template
    - The Docker Agent Template is the Container which will be started to handle your build process.
    - Click Docker Agent templates... and then Add Docker Template. You can now configure the container options.
    - Set the label of the agent to docker-agent. This is used by the Jenkins builds to indicate it should be built via the Docker Agent we're defining.
    - For the Docker Image, use benhall/dind-jenkins-agent:v2. This image is configured with a Docker client and available at https://hub.docker.com/r/benhall/dind-jenkins-agent/
    - Under Container Settings, In the "Volumes" text box enter /var/run/docker.sock:/var/run/docker.sock. This allows our build container to communicate with the host.
    - For Connect Method select Connect with SSH. The image is based on the Jenkins SSH Slave image meaning the default Inject SSH key will handle the authenication.
    - Make sure it is Enabled.
    - Click Save.
    - Jenkins can now start a Build Agent as a container when required.
    
# Create Build Project
  - This step creates a new project which Jenkins will build via our new agent. The project source code is at https://github.com/katacoda/katacoda-jenkins-demo. The repository has a Dockerfile; this defines the instructions on how to produce the Docker Image. Jenkins doesn't need to know the details of how our project is built.

  - Task: Create New Job
    - On the Jenkins dashboard, select Create new jobs
    - Give the job a friendly name such as Katacoda Jenkins Demo, select Freestyle project then click OK.
    - The build will depend on having access to Docker. Using the "Restrict where this project can be run" we can define the label we set of our configured Docker agent. The set "Label Expression" to docker-agent. You should have a configuration of "Label is serviced by no nodes and 1 cloud".
    
# Build Project
  - We now have a configured job that will build Docker Images based on our Git repository. The next stage is to test and try it.

  - Task: Build
    - On the left-hand side, select Build Now. You should see a build scheduled with a message "(pending—Waiting for next available executor)".
    - In the background, Jenkins is launching the container and connecting to it via SSH. Sometimes this can take a while to configure the Docker Agent. The error "(pending—Jenkins doesn’t have label docker-agent)" is while Jenkins waits for the Docker Agent to start.

    - You can see the progress using docker logs --tail=10 jenkins

    -It's normal for this to take a few moments to complete.

# View Console Output
  - Once the build has completed you should see the Image and Tags using the Docker CLI docker images.
  - What was built into the Docker Image was a small HTTP server. You can launch it using: docker run -d -p 80:80 katacoda/jenkins-demo:latest
  - Using cURL you should see the server respond: curl host01
  - Jenkins will have the console output of our build, available via the dashboard. You should be able to access it below:
  - https://2886795344-8080-ollie02.environments.katacoda.com/job/Katacoda%20Jenkins%20Demo/1/console
  - If you rebuilt the project, you would see a version 2 image created and the :latest tag reattached.
