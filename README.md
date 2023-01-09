# bae-jenkins-reference-impl
Reference implementation for project 4 - Jenkins

## Requirements
* 2 VMs - ideally ubuntu 20.04 - one with a firewall rule allowing access on port 8080 and the other with rules allowing access on ports 8081-8083
* Existing Spring backend and React frontend

## Setting up Artifact Repo
* On one of the VM instances, install docker and add your user to the docker group:
```shell
curl https://get.docker.com | sudo bash
sudo usermod -aG docker ${USER}
```
* Launch a docker container running a nexus server:
```shell
docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus sonatype/nexus3
```
* Obtain the admin password from the nexus container and use it to sign in to nexus, accessed via port 8081
* Configure a jenkins user with a password, and at least permission to view, edit and create maven releases

## Setting up Jenkins
* Install jenkins on the other VM, and configure the initial installation
* Install maven on the jenkins machine, and place the settings.xml file in /home/jenkins/.m2/settings.xml (fill in the password in settings.xml with your password for the nexus user you created)
* Install the pipeline integration maven plugin
* Create a pipeline project, and set up a script which will build and deploy your spring backend - see the provided jenkinsfile for an example
