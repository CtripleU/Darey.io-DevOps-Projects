# EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP

## SIMULATING A TYPICAL CI/CD PIPELINE FOR A PHP BASED APPLICATION

As part of the ongoing infrastructure development with Ansible started from Project 11, you will be tasked to create a pipeline that simulates continuous integration and delivery. Target end to end CI/CD pipeline is represented by the diagram below. It is important to know that both Tooling and TODO Web Applications are based on an interpreted (scripting) language (PHP). It means, it can be deployed directly onto a server and will work without compiling the code to a machine language.

The problem with that approach is, it would be difficult to package and version the software for different releases. And so, in this project, we will be using a different approach for releases, rather than downloading directly from git, we will be using Ansible uri module.

![CI_CD-Pipeline-For-PHP-ToDo-Application](https://user-images.githubusercontent.com/34113547/170272724-e4a4fd2c-074e-4470-a813-8bebefd410bf.png)

### Set Up

To get started, we will focus on these environments initially.

- Ci
- Dev
- Pentest
- 
Both SIT – For System Integration Testing and UAT – User Acceptance Testing do not require a lot of extra installation or configuration. They are basically the webservers holding our applications. But Pentest – For Penetration testing is where we will conduct security related tests, so some other tools and specific configurations will be needed. In some cases, it will also be used for Performance and Load testing. Otherwise, that can also be a separate environment on its own. It all depends on decisions made by the company and the team running the show.

What we want to achieve, is having Nginx to serve as a reverse proxy for our sites and tools. Each environment setup is represented in the below table and diagrams:

<img width="832" alt="Environment-setup" src="https://user-images.githubusercontent.com/34113547/170277133-f77d2889-782e-4e58-bb5c-9011bce07d95.png">

**CI-Environment**
![Project-14-CI-Environment](https://user-images.githubusercontent.com/34113547/170277134-1a25a188-da92-4fbf-b7ce-623759114a96.png)

**Other Environments from Lower To Higher**
![Project-14-Pentest-Environment](https://user-images.githubusercontent.com/34113547/170277145-aa8e23b0-705a-4881-b5d6-d7bfd3dc82b7.png)

**DNS requirements**
Make DNS entries to create a subdomain for each environment. Assuming your main domain is darey.io

You should have a subdomains list like this:
![Screenshot from 2022-05-25 15-51-42](https://user-images.githubusercontent.com/34113547/170280285-cb818496-d93d-4f1d-a98a-6e4fc71f7a1b.png)
![Screenshot from 2022-05-25 15-52-04](https://user-images.githubusercontent.com/34113547/170280324-3ae38006-3449-4d7c-8849-a1cbd03d9013.png)

The Ansible Inventory should look like this:

#### ci inventory file

#### dev Inventory file

#### pentest inventory file:



### Ansible roles for CI environment

Now go ahead and Add two more roles to ansible:

- SonarQube (Scroll down to the Sonarqube section to see instructions on how to set up and configure SonarQube manually)
- Artifactory


### Configuring Ansible For Jenkins Deployment

In previous projects, you have been launching Ansible commands manually from a CLI. Now, with Jenkins, we will start running Ansible from Jenkins UI.

To do this,

1. Navigate to Jenkins URL

2. Install & Open Blue Ocean Jenkins Plugin

3. Create a new pipeline


#### Creeate Jenkinsfile

Inside the Ansible project, create a new directory "deploy" and start a new file Jenkinsfile inside the directory.

Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage.

Now go back into the Ansible pipeline in Jenkins, and select configure

Scroll down to Build Configuration section and specify the location of the Jenkinsfile at deploy/Jenkinsfile

Navigate back to the pipeline and click "Build now"

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from Blue Ocean interface.

1. Click on Blue Ocean

2. Select your project

3. Click on the play button against the branch


**QUICK TASK**

1. Create a pull request to merge the latest code into the main branch
2. After merging the PR, go back into your terminal and switch into the main branch.
3. Pull the latest change.
4. Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build and test stages)
   1. Package 
   2. Deploy 
   3. Clean up
5. Verify in Blue Ocean that all the stages are working, then merge your feature branch to the main branch
6. Eventually, your main branch should have a successful pipeline like this in blue ocean


### Running Ansible Playbook from Jenkins

1. Installing Ansible on Jenkins
2. Installing Ansible plugin in Jenkins UI
3. Creating Jenkinsfile from scratch. (Delete all you currently have in there and start all over to get Ansible to run successfully)
