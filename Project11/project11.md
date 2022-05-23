# Ansible – Automate Project 7 to 10

### INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

1. Update the Name tag on your Jenkins EC2 Instance from Project 9 to **Jenkins-Ansible**. This server will be used to run playbooks

2. Create a new repository on GitHub with the name **ansible-config-mgt**

3. Install Ansible

```
sudo apt update

sudo apt install ansible
```

![Screenshot from 2022-05-04 21-17-26](https://user-images.githubusercontent.com/34113547/169897315-9b2a31ff-178a-473a-ba2e-e3e41aaea723.png)


![Screenshot from 2022-05-04 21-18-16](https://user-images.githubusercontent.com/34113547/169897370-41df8185-ab3d-43d2-bc79-b33fe342a0c9.png)


4. Configure Jenkins build job to save your repository content every time you change it 

* Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

![Screenshot from 2022-05-22 20-42-41](https://user-images.githubusercontent.com/34113547/169897821-c938c752-22bf-47ce-be3f-db5479f228d6.png)

* Configure Webhook in GitHub and set webhook to trigger ansible build.

![Screenshot from 2022-05-22 20-41-38](https://user-images.githubusercontent.com/34113547/169897678-772fa9c2-d74e-401d-80e2-23d4fa72f10d.png)

* Configure a Post-build job to save all (**) files, like you did it in Project 9.

![Screenshot from 2022-05-22 20-47-22](https://user-images.githubusercontent.com/34113547/169898005-fa446465-ec07-417f-b16b-f1ea0c9e2bde.png)

![Screenshot from 2022-05-22 20-49-54](https://user-images.githubusercontent.com/34113547/169898024-2479e891-96d8-4bf5-9d54-c5a48658f015.png)

![Screenshot from 2022-05-22 20-54-55](https://user-images.githubusercontent.com/34113547/169898095-e7520e9f-df89-4f72-8c57-14578b2015b6.png)



