

## Vulnerable software

Buscar softwares instalados para ver si alguno es vulnerable

Linux:

```
dpkg -l
```

Windows

```
C:\Program Files
```

## Scheduled Tasks

En servidores windows y linux hay una forma de que ciertos scripts se ejecuten cada x tiempo, uno de ellos seria con un antivirus que revise cada 1 hora o una app para hacer backups cada 30 min, lo que queremos es poder añadri una task con nuestro codigo malicioso para que se ejecute como un scheduled task(windows) o cronjons (Linux)

Directorios de cronjobs:

1. `/etc/crontab`
2. `/etc/cron.d`
3. `/var/spool/cron/crontabs/root`

## SSH Keys

`/home/user/.ssh/id_rsa` 
`/root/.ssh/id_rsa`

si la obtenemos de /root podemos usar el -i flag para loguearnos de esta forma :

```
bonack@htb[/htb]$ vim id_rsa
bonack@htb[/htb]$ chmod 600 id_rsa
bonack@htb[/htb]$ ssh root@10.10.10.10 -i id_rsa
```
Si tenemos permisos de escritura en algun directorio de claves ssh podemos meter nuestra propia clave para que sea valida en este directorio:

`/home/user/.ssh/authorized_keys`

la clave que pondremos ahí se genera con : 

```shell-session
bonack@htb[/htb]$ ssh-keygen -f key
```

esto nos dará dos archivos el key que es para usarse con `ssh -i` y el key.pub que es el que tenemos que poner en el directorio de claves ssh:

```shell-session
user@remotehost$ echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys
```
para luego hacer :

```shell-session
bonack@htb[/htb]$ ssh root@10.10.10.10 -i key
```

