# Architecture avec Docker

![Contribution guidelines for this project](doc/schema.svg)

### 1 Editez vos hosts

Ouvrez votre fichier `hosts`

````
sudo nano /etc/hosts
````

Ajoutez le site web du projet, exemple `local.exemple.com`

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

# My Docker projects
127.0.0.1  local.exemple.com
````
### 2 Ajouter votre configuration au proxy `Nginx`

Editer votre docker-config avec les variables d'environnement 

````
services:

  webapp:
    environment:
      # indispensable
      - "NGINX_HOST=local.exemple.com"
      # optionnel
      - "NGINX_SET_HEADER=WWW-Authorization 'Basic ZWxhc3RpYzplbGFzdGlj',Authorization 'Basic ZWxhc3RpYzplbGFzdGlj'"
      - "NGINX_CONNECTION_TIMEOUT=5"
````