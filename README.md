# Docker for Proximis [![Build Status](https://travis-ci.org/EmakinaFR/docker-proximis.svg?branch=master)](https://travis-ci.org/EmakinaFR/docker-proximis)
This repository allows the creation of a Docker environment that meets
[Proximis Omnichannel](https://www.proximis.com/solution-online/) requirements.

## Architecture
Here are the environment containers:

* `nginx`: [nginx:stable](https://hub.docker.com/_/nginx/) image.
* `php`: [php:7.1-fpm](https://hub.docker.com/_/php/) images.
* `mysql`: [mariadb:latest](https://hub.docker.com/_/mariadb/) image.
* `elasticsearch`: [elasticsearch:5.2.0](https://hub.docker.com/_/elasticsearch/) image.
* `redis`: [redis:latest](https://hub.docker.com/_/redis/) image.
* `maildev`: [djfarrelly/maildev:latest](https://hub.docker.com/r/djfarrelly/maildev/) image.

```bash
$ docker-compose ps
------------------------------------------------------------------------------------------------------------------------
dockerproximis_elasticsearch_1   /docker-entrypoint.sh elas ...   Up      0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp
dockerproximis_maildev_1         bin/maildev --web 80 --smtp 25   Up      25/tcp, 0.0.0.0:1080->80/tcp
dockerproximis_mysql_1           docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp
dockerproximis_nginx_1           nginx -g daemon off;             Up      443/tcp, 0.0.0.0:80->80/tcp
dockerproximis_php_1             docker-php-entrypoint php-fpm    Up      9000/tcp
dockerproximis_redis_1           docker-entrypoint.sh redis ...   Up      0.0.0.0:6379->6379/tcp
```

## Installation
This process assumes that depending of your OS, [Docker for Mac](https://www.docker.com/products/docker#/mac), [Docker for Windows](https://www.docker.com/products/docker#/windows) or [Docker for linux](https://www.docker.com/products/docker#/linux) is installed.
Otherwise, [Docker Toolbox](https://www.docker.com/toolbox) should be installed before proceeding.
The path to the local shared folder is `~/www` by default.

After the installation, the Proximis application is reachable by using [`http://proximis.dev/`](http://proximis.dev/).

### Clone the repository
```bash
$ git clone git@github.com:EmakinaFR/docker-proximis.git
```
It's also possible to download this repository as a
[ZIP archive](https://github.com/ajardin/docker-proximis/archive/master.zip).

### Define the environment variables
```bash
$ cp docker-env.dist docker-env
$ nano docker-env
```
The only mandatory environment variable is: __MYSQL_ROOT_PASSWORD__.

### Start the environment

#### With Docker for mac/windows/linux
```bash
$ docker-compose start
```
#### With Docker Toolbox

##### Create the virtual machine
```bash
$ docker-machine create --driver=virtualbox proximis
$ eval "$(docker-machine env proximis)"
```
##### Build the environment
```bash
$ docker-compose up -d
```

### Update the `/etc/hosts` file
```bash
$ docker-machine ip proximis | sudo sh -c 'echo "$(awk {"print $1"})  proximis.dev" >> /etc/hosts'
```
This command add automatically the virtual machine IP address in the `/etc/hosts` file.

### Configuration of the Proximis project
This operation can be achieved through the PHP-FPM container.
```bash
$ docker exec -it proximis_php_1 /bin/bash
```
Once in the container, the [official documentation](http://doc.change-commerce.com/) explains all the remaining steps.

## Custom configuration
The path to the local shared folder can be changed by editing the `docker-compose.yml` file.

It's also possible to automatically define Nginx servers and the PHP configuration. When the environment is built:

* `*.conf` files located under the `nginx` directory are copied to `/etc/nginx/conf.d/`,
* `*.ini` files located under the `php` directory are copied to `/usr/local/etc/php/conf.d/`.
