# DEVOPS TOOLING WEBSITE SOLUTION

This project makes use of the following tools/technologies:
1. Jenkins 
2. Kubernetes 
3. Jfrog Artifactory 
4. Rancher
5. Grafana
6. Prometheus
7. Kibana

The project will consist of:
Infrastructure: AWS
Webserver Linux: Red Hat Enterprise Linux 8
Database Server: Ubuntu 20.04 + MySQL
Storage Server: Red Hat Enterprise Linux 8 + NFS Server
Programming Language: PHP
Code Repository: GitHub

### Step 1: Prepare NFS Server
* Spin up a new EC2 instance with RHEL Linux 8 Operating System

* Configure LVM on the Server

Note: Instead of formating the disks as ext4 you will have to format them as xfs. There should be 3 Logical Volumes. lv-opt lv-apps, and lv-logs
Create mount points on /mnt directory for the logical volumes as follow:
Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8





