# Vagrant Shell - nginx script

**To provision your Vagrant VM to run nginx automatically by running a bash script on 'vagrant up', you can follow these steps:**

1. First, we create a file on the wished directory, so make sure to `cd` into your file first, then create vagrant file by running command:

````
vagrant init
````
2. Second, we need the provision file with the script inside it, so let's create the file by running `nano provision.sh`. This opens the nano editor and let's you put the script. The script we want is the one to run nginx, as follows:

````
#!/bin/bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install nginx -y
sudo systemctl restart nginx
sudo systemctl enable nginx
````

3. Now, we must add a line of code on VSCode to our previously created vagrant file: The whole file should look like this after you added the shell line:

````
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.10.100"
  config.vm.provision "shell", path: "provision.sh"
end
````

3. Now, we can run command: 
````
vagrant up
````
, and if there are no blokers and the script is working, the ip address on the code should open the nginx welcome page.

# App deployment

**After automating the installation of nginx using a shell file on our VM, let's now look at the steps to sync an app file to our VM, then later we will look at how we can automate the deployment of the app using a script.**

1. To start, we need to locate the files "app" and "environment" with the needed files and configurations, and move it into the same directory as the "VagrantFile"

2. Then let's sync the app file; on VSCode, add this line of code to your Vagrantfile:
```
config.vm.synced_folder "app", "/home/vagrant/app"
```
and it should all look like this:

````
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.10.100"
  # provisioning the vm
  config.vm.provision "shell", path: "provision.sh"
  # syncing app folder
  config.vm.synced_folder "app", "/home/vagrant/app"
end
````

3. Connect your VM to SSH (Linux) on a separate Bash terminal using command `vagrant ssh`

- Once you runned `ls` to verify if the app file is there, let's install nodejs version 6 and pm2 in our VM. Here are the steps:

1. Run command `sudo apt-get install nodejs -y` to install nodejs
2. Run `sudo apt-get install python-software-properties -y` to get a pack of some of the tools that we need
3. Run `curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -` to get the version 6 of nodejs
4. Run `sudo apt-get install nodejs -y` to basically upgrade to version to 6
5. Run `sudo npm install pm2 -g` to install a Nodje Package Manager (npm)

6. Now `cd` into the app folder

7. Run `npm install`
8. Finally, run `node app.js`

If there are no blockers, you should see this message:

![2023-04-18](https://user-images.githubusercontent.com/129942042/232794056-1afcf9a6-b2da-48bd-8a3a-761e1cf5370b.png)

9. To check if the app is running, grab the ip address from the Vagrantfile and add `:3000` at the end. Then put that on your browser and navigate, and if everything is ok, this is what it looks like:

![2023-04-18 (1)](https://user-images.githubusercontent.com/129942042/232802168-34f3481c-7cd3-4c41-8353-275acd1ae533.png)

# Provision - Nodejs and pm2 script

**I want to add the nodejs and pm2 installation scrip to my `provision.sh` file, so that when I `vagrant up`, it will get installed automatically. Let's look at the steps to achieve this:**

1. On the Bash terminal, press CTRL + S lo leave the Linux environment and get back to the main user at your local machine

2. `cd` into where you located your provision.sh file

3. Run `nano provision.sh` to open the nano editor and edit the file

4. Now add the following lines of code to your script:
````
sudo apt-get install python-software-properties -y
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y
sudo npm install pm2 -g
cd app
npm install
node app.js
````
, then CTRL + S to save and CTRL + X to exit.

5. If you do `cat provision.sh`, it should look like this:

![2023-04-18 (3)](https://user-images.githubusercontent.com/129942042/232824373-7b34695e-dc40-4f01-95b6-15ecb766e231.png)

6. Now all that is left, is to check it: Run `vagrant up`, then `vagrant ssh`.

7. Then, grab that ip address the same way as before and check is the app is working!

## MongoDB Provisioning

**Here are the steps to provision the installation of MongoDB on our db virtual machine:**

- Make sure to `vagrant destroy db` before doing this.

1. Create a new file on the same directory as the previous provision file called:
````
provision_db.sh
````

2. Add the following script to the file:
````
sudo apt update -y
sudo apt upgrade -y
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927
echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
sudo apt update -y
sudo apt upgrade -y
sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20
sudo systemctl start mongod
````

3. Then on the terminal on VSCode, do 
````
vagrant up db
````

4. Then, on Bash terminal do
````
vagrant ssh
````

5. Finally, to check it has been done properly run
````
sudo systemctl status mongod
````

And if the script was properly done, MongoDB shows as active and running.

## Connect DB machine and App machine

1. Open two bash terminals, one for the app VM and another for the db VM

2. Change the user name on the Vagrant file for both VM's from `xenial64` to `bionic64`, it should look like this:

![2023-04-20](https://user-images.githubusercontent.com/129942042/233350962-3844469a-cf0b-4d21-8a92-80f0c3eb4bd4.png)

3. Do `vagrant up` for both VMs on Vscode terminal

4. On one terminal run `vagrant ssh db` and for the other `vagrant ssh app`, so that both machines are connected with Linux

- **On your db vm terminal:**

5. Run `sudo nano /etc/mongod.conf`. This file already exists with the default configs of mongod. We just need to edit the PID inside it, so we open the nano editor.

6. At the bottom of the file, change the pid to `0.0.0.0`. This is so that any IP address can access it.

7. To restart and enable mongod so that it reflects our changes, run:
````
sudo systemctl restart mongod
sudo systemctl enable mongod
````
- **Now, on your app vm, we must connect to the correct environment, seed the database, and config the env so that it pulls info from our seeded db:**

8. Run `sudo nano .bashrc` - to open and edit our persist environment

9. At the end, we need to add a line with the following code:
````
export DB_HOST=mongod://192.168.10.150:27017/posts
````
 - We use the `export` command to create a variable in this environment.
 - This code basically says that if there is an env w the name `DB_HOST`, read it and try to connect to it on our ip with the mongod port number and go to posts page (pulls info from a random db) which is the collection of database that we want to access.

10. Make sure to CTRL + S to save and CTRL + X to exit.

11. Run `source .bashrc` to update and "source" the environment with new info we added, which is the db_host variable. To check it, you can run `printenv DB_HOST`

12. Now, `cd` into app

13. Run `npm install` to install the Node Package Manager, to run our app

14. To seed the database (put in the post page all the info we need from mongodb db), run:
````
node seeds/seed.js
````

15. Finnaly, we can run the node.js file:
````
 node app.js
 ````
 
 16. Now, to check everything went as it was supposed to, grab the correct ip address which is
 ````
 http://192.168.10.100:3000/posts
 ````
 , and this is what you should see (remember that the info will vary each time you load because it randomly grabs info from the db):
 ![2023-04-20 (1)](https://user-images.githubusercontent.com/129942042/233356211-52aabdec-9445-4026-9210-7867362a54d5.png)

## Provision the new config for the mongod.conf file
- This basically automates what we did manually on the db vm terminal:

1. First, we need to use the `echo` command, to overwrite the value of `bindIp`. On your `provision_db.sh` file (or however you named it) This is the code we need:
```
echo "bindIp: 0.0.0.0" | sudo tee -a /etc/mongod.conf
````
- It will change the ip address to 0.0.0.0 so that any ip address can have access to the app.

2. We also need to restart and enable mongod with the new ip address, so add the following code to the file:
````
sudo systemctl restart mongod
sudo systemctl enable mongod
````
- This is how the the `provision_db.sh` file should look like then:

![2023-04-20 (3)](https://user-images.githubusercontent.com/129942042/233379516-cb78ae46-51c1-4c26-991e-56b970910d89.png)

## Provision setting of environment and db seeding
- This part automates what we did on the app vm terminal:

1. I will use the same `echo` command, but his time to add a line to the `.bashrc` file. To do this we also need a `>>` sign to tell the program to add the line to the file. Add this line to your `provision.sh` file:
````
echo 'export DB_HOST=mongodb://192.168.10.150:27017/posts' >> ~/.bashrc
````

2. Add again, add the same `source` command to update and source the env with the new changes:
````
source ~/.bashrc
````
3. We need to add the other commands we runned mannually as well after we `cd` into app, so the code is as follows:
````
cd app
npm install
node seeds/seed.js
node app.js
pm2 start app.js
````
- I added `pm2 start app.js` so that the app can run in the background. At the end your `provision.sh` file should look like this:

![2023-04-20 (4)](https://user-images.githubusercontent.com/129942042/233389365-26c6e516-9682-4d22-8098-b80dc76b5b53.png)

4. The next step is to check if everything is working. For me, the best is to:

 i. assure your files are saved
 
 ii. restart your laptop
 
 iii. vagrant destroy (assure both machines are destroyed on VirtualB)
 
 iv. vagrant up (assure both machines are running on VirtualB)
 
 v. browse the ip `192.168.10.100` to check nginx is installed
 
 vi. browse the ip `192.168.10.100:3000` to check if app is running
 
 vii. browse the ip `192.168.10.100:3000/posts` to check if mongodb is running and post page is available
