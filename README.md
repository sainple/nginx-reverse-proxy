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

Le serveur proxy `Nginx` tourne sur le port `80`. Celui-ci contact vos projets web Docker.
Exemple de configuration `Nginx`. Créer votre configuration dans le dossier `conf.d` du Docker `reverse proxy` de `Nginx`. 

````
server {
    listen        80;
    server_name   local.exemple.com;
    
    location / {
    proxy_pass              http://172.20.0.9:80;
    proxy_redirect          off;
    proxy_set_header        Host $host;
    proxy_set_header        X-Real-IP $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header        X-Forwarded-Host $server_name;
    proxy_connect_timeout   60;
    }
}
````

### 3 Relancer votre service Nginx après l'ajout d'une nouvelle configuration

````
docker exec nginx nginx -s reload
````