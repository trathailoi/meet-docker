# 9thwonder
Automate nginx & ssl for multiples virtual hosts.
## Start the auto creation & renewal ssl certificate using Nginx & Let's Encrypt
Lets create a global network (inside of your docker host) first. This network will be shared for all services in different directories in you docker host.

``` bash
docker network create nginx-proxy-net
```

then start an nginx service which will take care of all the other stuff related to virtual hosts and ssl certificate for you. And of course it will also be joined into the network.

``` bash
docker-compose up -d
```

From now on, whenever you create a new site in a container, that container should be joined into the global network `nginx-proxy-net` to be able to claim the virtual host and ssl certificate.

## Create a new dev site
Lets create a new site (with ssl installed already) from a template that I made myself for 9thwonder development process. Don't forget to replace these variables `<project-name>` and `<buil-id-number>` below with your real stuff, or else, of course, things won't work (có làm mới có ăn).

### Watch out!! The generated docker-compose.yml file will pull the image with the name under the format `<project-name>:<buil-id-number>`. Be aware of that. It is better to go get your coffee before doing these kind of tasks.

``` bash
# create new folder for you new site
mkdir <your new site> && cd <your new site>
# then generate a docker-compose.yml file into that folder, using the command below
docker run --rm -i brettmc/docker-compose-generator generate \
-e PROJECT_NAME=<project-name> \
-e BUILD_ID=<build-id-number (will be used for docker image tag)> \
< ../compose-template.yml \
> docker-compose.yml
```

Lets have a peek on the generated docker-compose file to see if it is good to go.

``` bash
docker-compose config
```

Optionally, you can add a `.env` file in this folder to be able to change these configs:

``` env
MYSQL_DATABASE=wordpress
MYSQL_USER=example_user
MYSQL_PASSWORD=iii3studi1
```

If everything is beautiful enough. Lets rock!

``` bash
docker-compose up -d
```

And voilah! It should be working on `https://<project-name>-<build-id-number>.dev9w.xyz`, open your browser and check it out. That's it!
