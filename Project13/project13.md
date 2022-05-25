# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES

Static assignments use import Ansible module. The module that enables dynamic assignments is include.

Hence,
```
import = Static
include = Dynamic
```
When the import module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when you execute **site.yml** playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during execution will be used.

Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic ones, it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.


## Introducing Dynamic Assignment Into Our structure

* In your https://github.com/<your-name>/ansible-config-mgt GitHub repository start a new branch and name it **dynamic-assignments**.

![Screenshot from 2022-05-25 01-04-07](https://user-images.githubusercontent.com/34113547/170198278-f5c1ed3a-ada7-4054-a7eb-2f28d012233b.png)

![Screenshot from 2022-05-25 01-06-59](https://user-images.githubusercontent.com/34113547/170198078-acf17d15-5236-4b0a-bcb4-7d34ceeeb31b.png)


* Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook later. For now, let us keep building up the structure.

Your GitHub shall have following structure by now:
![Screenshot from 2022-05-25 01-07-23](https://user-images.githubusercontent.com/34113547/170199266-39a232ca-f678-48f2-b545-69b0d149d6b7.png)

Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

Your layout should now look like this.
![Screenshot from 2022-05-25 05-28-27](https://user-images.githubusercontent.com/34113547/170199317-2ffa3a5e-d189-40fe-8a56-6e798859c595.png)
  
Now paste the instruction below into the env-vars.yml file.

![Screenshot from 2022-05-25 05-44-24](https://user-images.githubusercontent.com/34113547/170199358-68da185e-a190-43b1-8b0a-2fb2d292b4c4.png)

Notice 3 things to notice here:

1. We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:
  * include_role
  * include_tasks
  * include_vars

  In the same version, variants of import were also introduces, such as:
  * import_role
  * import_tasks

2. We made use of a special variables `{ playbook_dir }` and `{ inventory_file }`. `{ playbook_dir }` will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. `{ inventory_file }` on the other hand will dynamically resolve to the name of the inventory file being used, then append **.yml** so that it picks up the required file within the env-vars folder.

3. We are including the variables using a loop. **with_first_found** implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.


## Update site.yml with dynamic assignments
  
Update site.yml file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)

site.yml should now look like this.
![Screenshot from 2022-05-25 05-44-50](https://user-images.githubusercontent.com/34113547/170201386-9f052abe-d436-4286-94ef-e2b048045ab3.png)

### Community Roles
You need to create a role for MySQL database – it should install the MySQL package, create a database and configure users. There are tons of roles that have already been developed by other open source engineers out there. These roles are actually production ready, and dynamic to accomodate most of Linux flavours. With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.
  
  
## Download MySQL Ansible role

```
git branch roles-feature
git switch roles-feature
```
![Screenshot from 2022-05-25 05-54-42](https://user-images.githubusercontent.com/34113547/170201873-dabf07dd-0af0-43fd-a546-b8ee81e50d33.png)

Inside roles directory create your new MySQL role and rename the folder to mysql with 
```
ansible-galaxy install geerlingguy.mysql

mv geerlingguy.mysql/ mysql
```
![Screenshot from 2022-05-25 05-54-42](https://user-images.githubusercontent.com/34113547/170201563-e1faf092-8d45-490e-b50e-fbf00017e231.png)

Read the README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.

![Screenshot from 2022-05-25 06-38-03](https://user-images.githubusercontent.com/34113547/170204474-fcc149c7-2f0e-436a-81c9-7a53eec6b71c.png)

Now, if you are satisfied with your codes, you can create a Pull Request and merge it to main branch on GitHub

![Screenshot from 2022-05-25 06-05-44](https://user-images.githubusercontent.com/34113547/170204185-3cc68c05-812f-44d4-999a-7e056c8ca66b.png)

![Screenshot from 2022-05-25 06-07-35](https://user-images.githubusercontent.com/34113547/170204203-1634fd0c-c430-4bee-a85b-a0db970bb702.png)

  
## Load Balancer roles
  
To able to choose which Load Balancer to use, Nginx or Apache, we need to have two roles respectively:

- Nginx
- Apache

 
* Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one – this is where you can make use of variables.

* Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.

* Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.

* Declare another variable in both roles load_balancer_is_required and set its value to false as well

![Screenshot from 2022-05-25 07-00-04](https://user-images.githubusercontent.com/34113547/170204884-df3e0844-9d8a-4a7f-9a9d-2bb7b9dd52c7.png)

![Screenshot from 2022-05-25 07-00-44](https://user-images.githubusercontent.com/34113547/170204937-6d8999eb-468e-4266-850b-3fd24880399f.png)

* Update both assignment and site.yml files respectively

loadbalancers.yml file:
![Screenshot from 2022-05-25 07-18-02](https://user-images.githubusercontent.com/34113547/170205007-35df0942-5abe-4d68-a1a0-853bb52bf44f.png)

site.yml file
![Screenshot from 2022-05-25 07-23-34](https://user-images.githubusercontent.com/34113547/170205144-309edbe6-edbd-4996-b2a9-d625b3b0b1b4.png)

Use the env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

Activate load balancer and enable nginx by setting these in the respective environment’s env-vars file.
![Screenshot from 2022-05-25 07-27-31](https://user-images.githubusercontent.com/34113547/170205363-09cdbd0d-d34b-4c84-a452-f6c65c879cbc.png)

You can do the same with apache LB and also switch it by setting respective environmental variable to true and other to false.
  
To test this, you can update inventory for each environment and run Ansible against each environment.
![Screenshot from 2022-05-25 10-15-25](https://user-images.githubusercontent.com/34113547/170215099-7adc3452-c9e1-431f-b741-247926af4ea4.png)

![Screenshot from 2022-05-25 08-18-36](https://user-images.githubusercontent.com/34113547/170215460-e6b070cb-cffc-4907-8778-b686607f6309.png)
![Screenshot from 2022-05-25 08-18-50](https://user-images.githubusercontent.com/34113547/170215923-aad4e998-c889-4083-a3b1-ab6b8c3cb36d.png)
![Screenshot from 2022-05-25 08-19-44](https://user-images.githubusercontent.com/34113547/170215922-7b14a030-2b7e-46b7-aa0c-be8e4b90457e.png)
![Screenshot from 2022-05-25 08-20-00](https://user-images.githubusercontent.com/34113547/170215948-78aa6543-379c-45ac-ba92-748d16bf627e.png)


