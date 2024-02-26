I have lifted most of the underlying scripts from https://github.com/jasonmccreary/local-docker-stack, and changed only docker_compose.yml for my purpoes.

## Deploy the LAMP stack

Clone this repo into a dir on your laptop. cd into the dir and in the lamp dir, then run `docker compose up -d`.

## Control the LAMP stack

```
docker ps				# Get a list of docker containers you have running
docker exec -it CONTAINER_ID sh		# Shell into a container
docker compose down			# Turn off the container
```

## Deploy a PHP web app

There is a one-pager in /html, and the lamp stack has a volume pointing to it. Once the LAMP stack is running, you should be able to see it at http://localhost/webapp/.

You can clone your own PHP web app into /html now. You might need to fiddle with paths to client-side assets. 

Want to try my login-demo? You'll need [Composer](http://getcomposer.org) to install dependencies. Clone https://github.com/pzzd/not-so-great-login-demo into /html/not-so-great-login-demo, cd into not-so-great-login-demo, and run `composer install`. 
