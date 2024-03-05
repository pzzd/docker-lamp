# docker-lamp

This is a project for running a PHP and MySQL app from Docker containers. The LAMP stack is defined in docker-composer.yml. Directories for external volumes are already defined, so you only need to run the containers, deploy your PHP code to the right place, and wire the web app to the database.

I have lifted most of the LAMP set-up scripts from https://github.com/jasonmccreary/local-docker-stack, and changed only docker_compose.yml for my purposes.

The structure of this project is the following:
- data: An external volume for mysql data. This will fill automatically when you create a database, and the data will persist when you shut down the Docker container.
- html: An external volume for PHP code. There is a single HTML file here. You can clone a PHP app repo here too.
- lamp: A directory for holding the Docker container info and scripts.

None of this code is production-ready. Please use with caution.

## Deploy the LAMP stack

Clone this repo into a dir on your laptop. Cd into /lamp dir. Create an empty apache/vhosts.conf file and then build the Docker containers.
```
cd lamp
touch apache/vhosts.conf
docker compose up -d
```

### Control the LAMP stack

```
docker compose up -d			# Run the containers defined in docker-compose.yml
docker ps				# Get a list of containers you have running
docker exec -it CONTAINER_ID sh		# Shell into a container
docker compose down			# Turn off the container
```

## Add users to MySQL

Log in as the root user (creds are buried in /lamp) using Docker Desktop.
```
mysql -u root -pXXXXXXX
```
Give dbuser privileges to do things.
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'dbuser'@'%';
```

Now you can log in to mysql in Docker Desktop with `mysql -u dbuser -pXXXXXXXX`.


## Deploy a PHP web app

There is a one-pager in /html, and the lamp stack has a volume pointing to it. Once the LAMP stack is running, you should be able to see it at http://localhost/webapp/.

You can clone your own PHP web app into /html now. You might need to fiddle with paths to client-side assets. 

### A specific example 

Want to try my login-demo? You'll need [Composer](http://getcomposer.org) to install dependencies. Clone https://github.com/pzzd/not-so-great-login-demo into /html/not-so-great-login-demo, cd into not-so-great-login-demo, and run `composer install`.

#### Create the app database

Use the script at /database/login-demo-dump.sql to create a database and a table for the web app.

Set the username and password in /credentials/mysql.json.

#### Connect the app to the database

Get the mysql IP address inside the docker container:
```
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' lamp-db-1
172.21.0.2
```

Update /html/not-so-great-login-demo/credentials/mysql.json thusly:
```
"servername": "172.21.0.2:3306",
```  
You have to do this every time you do `docker compose up` because the IP address will change.

## TODO
- figure out why i need apache/vhosts.conf
- connect with mysqli with a fixed ip address?
- fix web app paths
- make fully self contained with working web app as part of container

## Thanks!

I found these resources useful.

- https://github.com/jasonmccreary/local-docker-stack
- https://www.howtogeek.com/devops/how-to-run-mysql-in-a-docker-container/ 
- https://www.baeldung.com/ops/docker-cant-connect-local-mysql
