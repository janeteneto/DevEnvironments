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
