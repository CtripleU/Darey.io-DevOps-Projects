# Ansible – Automate Project 7 to 10

### INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

1. Update the Name tag on your Jenkins EC2 Instance from Project 9 to **Jenkins-Ansible**. This server will be used to run playbooks

2. Create a new repository on GitHub with the name **ansible-config-mgt**

3. Install Ansible

```
sudo apt update

sudo apt install ansible
```

4. Configure Jenkins build job to save your repository content every time you change it 
