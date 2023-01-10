# bae-jenkins-reference-impl
Reference implementation for project 4 - Jenkins

## Overview
This project covers the usage of Jenkins as a CI server, and specifically the use of Jenkins in conjunction with npm and maven to build, artifact and deploy react and spring layers of a web application. Knowledge of react, java, npm and maven will be assumed, the principles of jenkins will be covered in the teaching around this project.

## Desired Outcome
The goal of this project is to successfully create the following:
* A Jenkins server, with a pipeline job which will build the react and spring applications for the project
* A nexus server (or other suitable artifact repository) to store build artifacts ready for deployment
* A production server, onto which Jenkins will pull the build artifacts and deploy the front- and back-end applications

## Requirements
* 3 VMs - ideally ubuntu 20.04 - one with a firewall rule allowing access on port 8080, one with rules allowing access on ports 8081-8083, and the other with rules allowing access on ports 8080 and 3000
* Existing Spring backend and React frontend

## Setting up Artifact Repo
### Start Nexus Server
* On one of the VM instances, install docker and add your user to the docker group:
```shell
curl https://get.docker.com | sudo bash
sudo usermod -aG docker ${USER}
```
* Launch a docker container running a nexus server:
```shell
docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus sonatype/nexus3
```
### Configure Nexus Server
* Obtain the admin password from the nexus container and use it to sign in to nexus, accessed via port 8081
* It is good practice to disable anonymous access to the nexus server, thus requiring authentication for all requests
* The server will begin with a number of hosted repositories by default, including maven repositories
* Add a new npm repository for your npm packages
* Configure a jenkins user with a password, and at least permission to view, edit and create maven releases and npm repositories
* Add 'npm bearer token' as an active realm on the nexus server, to enable authentication by npm later on.

## Setting up Jenkins
* Install jenkins on the other VM, and configure the initial installation
* Install maven on the jenkins machine, and place the settings.xml file in /home/jenkins/.m2/settings.xml (fill in the password in settings.xml with your password for the nexus user you created)
* Install npm on the jenkins server, and run the following command:
```shell
npm adduser --registry http://<nexus-machine-hostname>:8081/repository/<name-of-npm-repo>
```
This will prompt you for a username and password (should match the credentials for the nexus user you created) and an email (you can use a dummy email for this)
* Install the pipeline integration maven plugin
* Create a pipeline project and set up a script which will build your react frontend and spring backend, push them to the nexus server, and then deploy to a production server - see the provided jenkinsfile for an example

## Setting up Production Server
* On the production machine (with ports 8080 and 3000), install npm and set up credentials for the nexus repository as before.
* Ensure wget is installed; this will be used to retrieve the backend .war from nexus

## Notes
* At present, the spring and react apps used for this example do not actually connect to one another; connecting the two is assumed to be part of project 3. This project is concerned only with deploying them and, as such, separate reference apps are used for now to demonstrate the principles involved.