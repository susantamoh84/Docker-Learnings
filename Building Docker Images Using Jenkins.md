# Launch Jenkins
  - Launch Jenkins as a Docker Container with the following command:
  - docker run -d -u root --name jenkins \
      -p 8080:8080 -p 50000:50000 \
      -v /root/jenkins_2112:/var/jenkins_home \
      jenkins/jenkins:2.112-alpine

  -All plugins and configurations get persisted to the host (ssh root@host01) at _/root/jenkins2112. Port 8080 opens the web dashboard, 50000 is used to communicate with other Jenkins agents. Finally, the image has an alpine base to reduce the size footprint.
