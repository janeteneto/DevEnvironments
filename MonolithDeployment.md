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
sudo apt-get install nodejs -
sudo npm install pm2 -g
npm install
node app.js
````
, then CTRL + S to save and CTRL + X to exit.

5. If you do `cat provision.sh`, it should look like this:


