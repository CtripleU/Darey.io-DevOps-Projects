# AUX PROJECT 1: SHELL SCRIPTING

**Project details** - Onboard 20 new Linux users onto a server. Create a shell script that reads a csv file that contains the first name of the users to be onboarded.

1. Create the project folder called **Shell**

`mkdir Shell` and `cd Shell`

2. Create a csv file **names.csv** and insert some names into it. Put one name on each line

`touch names.csv`

`vim names.csv`

3. Create a group called developers

`sudo groupadd developers`

4. Update current user with the correct public key and private key.

`cd .ssh`

`touch id_rsa.pub` This is a file for the public key

`touch id_rsa` This is a file for the private key

5. Check the list of current users

6. Write a script that reads the **names.csv** file and creates new users, adds a password to each user, adds the user to the **developers** group previosly created, and copies the sudo user’s SSH configuration to the new user

`touch onboarding-users.sh`

7. Make the script executable 

`chmod +x onboarding-users.sh`

8. Run the script 

`sudo ./onboarding-users.sh`


9. List users on the server


As shown, the users have been successfully created.
