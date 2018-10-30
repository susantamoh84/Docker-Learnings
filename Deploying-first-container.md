What Is Docker?
Docker describes themselves as "an open platform for developers and sysadmins to build, ship, and run distributed applications".

Docker allows you to run containers. A container is a sandboxed process running an application and its dependencies on the 
host operating system. The application inside the container considers itself to be the only process running on the machine 
while the machine can run multiple containers independently.

In this first scenario, you'll take the role of Jane, a developer who needs to deploy a new Key-Value Store for an application 
she's working with. After discussions, it's been decided to use Redis, a popular KV Store.

Jane is unfamiliar with how Redis is deployed but has heard Docker makes it straightforward to deploy services into development 
and production.

This scenario discusses how she will complete her task and deploy Redis as a Docker Container.

Jane's development environment has access to latest version of the Docker Engine via a machine called docker. Her local dev 
machine has the Docker Client installed and accessible via the command line.

Step1: Running a container - 

 - Search & find existing images: docker search <name>: docker search redis
 - Run a service in background: docker run <options> <image-name> : docker run -d redis
 - Run a specific image: docker run -d redis:latest
 - Running processes: docker ps
 - Details about docker running container: docker inspect <friendly-name|container-id>:  docker inspect cb651d301476
 - Checking logs of running container: docker logs <friendly-name|container-id>: docker logs cb651d301476
 - To make the service accessible, expose the ports: docker run -d --name redisHostPort -p 6379:6379 redis:latest
 
