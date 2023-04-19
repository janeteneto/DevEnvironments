## How do we run a process in the background in Linux?

Running a process in the backgroung allows you to run other commands or processes in the same terminal witouth having to wait for the first one to finish without interrupting the running process.

The simplest way of doing this is to add a **`&`** at the end of the command:

1. On your `provision.sh` file, change the version of node to version 7.x

2. Now, on your Bash terminal, as usual, do:
````
vagrant up app
````

3. 
````
vagrant ssh app
````
4. Now `cd` into the app file

5. Run this command to run the app in the background:
````
node app.js &
````
6. After this, press enter to go back to the terminal 


## Using pm2 to run nodejs app as a background process
- There is another method of doing this with a `pm2 ` command:

1. Repeat all the steps of the other method from 1 to 4

2. Run this command to start the pm2 process:
````
pm2 start app.js
````
