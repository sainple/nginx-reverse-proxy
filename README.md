# Architecture Nginx reverse proxy for multi containers

> 
todo: do the https and letsencrypt

![Contribution guidelines for this project](doc/schema.svg)

### 1 Edit your hosts

Open your file `hosts`

````
sudo nano /etc/hosts
````

Add the project website, example `local.example.com`

````
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1       localhost
255.255.255.255 broadcasthost
::1             localhost
fe80::1%lo0     localhost

# My Dockers projects
127.0.0.1  local.example.com
````
### 2 Configure the `docker-compose` of your web project for` Nginx`

Edit environment variables. Also add the link of the Nginx network.
`NGINX_HOST = local.example.com` is the example of the documentation.

````
services:

  my_webapp:
    environment:
      # required
      - "NGINX_HOST=local.example.com"
      # optional
      - "NGINX_SET_HEADER=WWW-Authorization 'Basic 123456789',Authorization 'Basic 123456789'"
      - "NGINX_CONNECTION_TIMEOUT=5"
      - "NGINX_PORT=80"
      
      
networks:
  default:
    external:
      name: nginx-reverse-proxy_default
````

Env | require/optional | default | example
------------ | ------------- | ------------- | -------------
NGINX_HOST               | require   | `null` | `example.com`
NGINX_SET_HEADER         | optional  | `null` | `WWW-Authorization 'Basic 123456789',Authorization 'Basic 123456789'`
NGINX_CONNECTION_TIMEOUT | optional  | `60`   | `600`
NGINX_PORT               | optional  | `80`   | `8080`

### 3 Launch your docker

````
docker-compose up -d
````

In the `default.conf` file you should have this

````
# host local.exemple.com
upstream  my_webapp { server xxx.xx.xx.xx:80; }
server {
    listen 80;
    server_name local.exemple.com;
    location / {
        proxy_pass              http://my_webapp;
        proxy_redirect          off;
        proxy_set_header        Host $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Host $server_name;
        # custom
        proxy_set_header        WWW-Authorization "Basic 123456789";
        proxy_set_header        Authorization "Basic 123456789";
        # custom
        proxy_connect_timeout   5;
    }
}
````

# Hello world !

Go to your favorite browser and open the url `http://local.exemple.com`. !
