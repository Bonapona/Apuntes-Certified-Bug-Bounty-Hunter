
## WordPress Structure

En htb se centran en la instalación LAMP (Linux,Apache,MySQL,PHP)

Todo se guarda en `/var/www/html`

### File structure

```shell-session
.
├── index.php
├── license.txt
├── readme.html
├── wp-activate.php
├── wp-admin
├── wp-blog-header.php
├── wp-comments-post.php
├── wp-config.php
├── wp-config-sample.php
├── wp-content
├── wp-cron.php
├── wp-includes
├── wp-links-opml.php
├── wp-load.php
├── wp-login.php
├── wp-mail.php
├── wp-settings.php
├── wp-signup.php
├── wp-trackback.php
└── xmlrpc.php
```

#### Los más importantes

- `index.php` is the homepage of WordPress.
- `license.txt` contains useful information such as the version WordPress installed.
- `wp-activate.php` is used for the email activation process when setting up a new WordPress site.
- `wp-admin` folder contains the login page for administrator access and the backend dashboard. Once a user has logged in, they can make changes to the site based on their assigned permissions. The login page can be located at one of the following paths:
    
    - `/wp-admin/login.php`
    - `/wp-admin/wp-login.php`
    - `/login.php`
    - `/wp-login.php`

This file can also be renamed to make it more challenging to find the login page.

- `xmlrpc.php` is a file representing a feature of WordPress that enables data to be transmitted with HTTP acting as the transport mechanism and XML as the encoding mechanism. This type of communication has been replaced by the WordPress [REST API](https://developer.wordpress.org/rest-api/reference)
- The `wp-config.php` file contains information required by WordPress to connect to the database, such as the database name, database host, username and password, authentication keys and salts, and the database table prefix. This configuration file can also be used to activate DEBUG mode, which can useful in troubleshooting
- The `wp-content` folder is the main directory where plugins and themes are stored. The subdirectory `uploads/` is usually where any files uploaded to the platform are stored. These directories and files should be carefully enumerated as they may lead to contain sensitive data that could lead to remote code execution or exploitation of other vulnerabilities or misconfigurations.
- `wp-includes` contains everything except for the administrative components and the themes that belong to the website. This is the directory where core files are stored, such as certificates, fonts, JavaScript files, and widgets.


### WordPress User Roles

| Role          | Description                                                                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Administrator | This user has access to administrative features within the website. This includes adding and deleting users and posts, as well as editing source code. |
| Editor        | An editor can publish and manage posts, including the posts of other users.                                                                            |
| Author        | Authors can publish and manage their own posts.                                                                                                        |
| Contributor   | These users can write and manage their own posts but cannot publish them.                                                                              |
| Subscriber    | These are normal users who can browse posts and edit their profiles.                                                                                   |

## WordPress Core Version/Plugins/Themes Enumeration

Pillar la versión,los plugins y los themes del html de la página, si no están tal cual mirar en los archivos css y js 
## Directory Indexing

Se ve que por mucho que tengas un plugin vulnerable desactivado se puede explotar porque como tal sigue instalado en la web, simplemente no se indexa , la forma segura de tenerlo sería o actualizado y desactivado o directamente eliminarlo
## User Enumeration

METODO 1: 

Si a un usuario se le asigna un ID peus solo hay que fuzzear : 

```shell-session
http://blog.inlanefreight.com/?author=1
```


METODO 2: 

Usar los endpoints de wp , antes de la versión 4.7.1 si accedías al enlace de debajo te listaba todos los usuarios.

```shell-session
 curl http://blog.inlanefreight.com/wp-json/wp/v2/users | jq
```


## RCE via the Theme Editor

SOLO si tenemos credenciales de admin

1. Appearance -> Theme editor 
<img width="1086" height="475" alt="image" src="https://github.com/user-attachments/assets/93b566dd-b71b-44a2-90b8-00b17cc112b9" />

 
2. Antes teníamos puesto el tema transportex así que tendremos que cambiarlo por un tema que no esté en uso como Twenty Seventeen en este caso
   <img width="1081" height="306" alt="image" src="https://github.com/user-attachments/assets/8e68c577-2886-4252-bec1-767c8862d11e" />


3. Ahora dentro de ese theme elige un archivo que no sea crítico como 404.php y metele dentro la web shell PHP
4. Acceder a 
 ````shell-session
http://<target>/wp-content/themes/twentyseventeen/404.php?cmd=id
````
