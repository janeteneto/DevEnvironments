# NGINX Reverse Proxy

**What is a port?**
- In simple terms, a port is like a virtual gate through which network traffic flows between a device and a network. Each network service listens to incoming traffic on a specific port assigned to it, for example, HTTTP is port 80 and SSH is port 22. Port numbers can range from 0 to 65535-

**What is a proxy, a reverse proxy, and how do they differ from each other?**
- A proxy server is an intermediary server between the user and a server, that makes requests to the server on behalf of the user.
- A reverse proxy, however, will act on behalf of the server instead to capture and send user requests to the server.
- The **main difference** between a proxy and a reverse proxy is the direction of the traffic flow. In a proxy, traffic flows from the client to the proxy and then to the server, while in a reverse proxy, traffic flows from the client to the reverse proxy and then to the server.

![User](https://user-images.githubusercontent.com/129942042/232843870-357e1677-e38a-4e62-94ab-cc6b4dfe8972.png)

**What is Nginx's default configuration, and what is it for?**
- Nginx's default configuration file sets some default values for important settings that control how Nginx works, such as the location of web files and the number of the port which is port 80 for HTTP. These configurations make sure that nginx works properly without the user having to manually set it up

**How do you set up a Nginx reverse proxy? Here are the steps:**

1. Make sure to `vagrant up` and then `vagrant ssh` first

2. Because we added the nginx script to our provision file, this should install automatically.

3. So now, we need to stop the nginx default configurations, because that is for a normal proxy. Run command:
````
sudo unlink /etc/nginx/sites-enabled/default
````

4. The next step is to run this command so that we can cd into this directory:
````
cd etc/nginx/sites-available/
````

5. Run command `nano reverse-proxy.conf` to create a .conf file and open nano editor

6. Now, copy the following script:
````
server {
   listen 80;
   server_name 192.168.10.100;

   location / {
       proxy_pass http://192.168.10.100:3000;
       proxy_set_header Host $host;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       proxy_set_header X-Forwarded-Proto $scheme;
   }
}
````

7. Press CTRL + S to save the file and CTRL +  to exit nano

8. Run this command to restart nginx:
````
sudo service nginx restart
````

9. Now copy the first ip address from the script and paste it into your browser to check if it worked. You should see the same Sparta App welcoming page as before.
