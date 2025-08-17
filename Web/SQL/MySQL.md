
## Comandos básicos

-Conectarse(te pedirá contraseña)
```
mysql -u <usuario> -p
```
-Conexión remota
```
mysql -u root -h <host> -P <puerto> -p
````
-Crear Database
```
CREATE DATABASE users;
```
-SHOW
```
SHOW DATABASES;
```
-Cambiar a una base de datos especifica
```
USE <nombre>;
```

## Tablas

-Crear Tabla
```
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
);
```
-Ver estructura de la tabla
```
DESCRIBE <tabla>;
```
## Propiedades de columnas en una tabla

**AUTO_INCREMENT**: Incrementa automáticamente el ID.
**NOT NULL**: Campo obligatorio.
**UNIQUE**: Asegura que no se repitan valores (ej. usuarios únicos).
**DEFAULT NOW()**: Fecha y hora actuales por defecto.
**PRIMARY KEY**: Clave primaria única para identificar cada registro.