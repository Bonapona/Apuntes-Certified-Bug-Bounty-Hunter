# STATEMENTS
## INSERT

(Permite agregar nuevos registros a una tabla)

-Sintaxis básica
```
INSERT INTO tabla VALUES (valor1, valor2, ...);
```
-Insertar solo algunas columnas (cuando hay valores por defecto)
```
INSERT INTO tabla(col1, col2) VALUES (val1, val2);
```
-Insertar múltiples filas
```
INSERT INTO tabla(col1, col2) VALUES (val1, val2), (val3, val4);
```

## SELECT

(Permite consultar datos de una tabla)

-Seleccionar todos los datos
```
SELECT * FROM tabla;
```
-Seleccionar columnas especificas
```
SELECT columna1, columna2 FROM tabla;
```

## DROP

-Eliminar una tabla
```
DROP TABLE nombre_tabla;
```
## ALTER

(Modifica la estructura de una tabla ya existente)

-Agregar una columna
```
ALTER TABLE tabla ADD nueva_columna TIPO;
```
-Renombrar una columna
```
ALTER TABLE tabla RENAME COLUMN vieja TO nueva;
```
-Modificar tipo de dato
```
ALTER TABLE tabla MODIFY columna NUEVO_TIPO;
```
-Eliminar una columna
```
ALTER TABLE tabla DROP columna;
```

## UPDATE

(Modifica los valores de filas existentes)

-Ejemplo
```
UPDATE tabla SET columna = 'nuevo_valor' WHERE condición;
```

# QUERY RESULTS

## ORDER BY

(Permite ordenar los resultados por una o más columnas)

-Ascendente
```
SELECT * FROM logins ORDER BY password;
```
-Descendente
```
SELECT * FROM logins ORDER BY password DESC;
```
-Ordenar por múltiples columnas
```
SELECT * FROM logins ORDER BY password DESC, id ASC;
```

## LIMIT

(Restringe el número de registros que devuelve una consulta)

-Limitar a 2 resultados
```
SELECT * FROM logins LIMIT 2;
```
-Usar desplazamiento (offset)
```
SELECT * FROM logins LIMIT 1, 2;
```
El primer número es el offset (empezando desde 0), y el segundo es la cantidad de filas a devolver

## WHERE

(Filtra los resultados basados en condiciones)

-Número
```
SELECT * FROM logins WHERE id > 1;
```
-String
```
SELECT * FROM logins WHERE username = 'admin';
```

## LIKE

(Permite buscar registros que coincidan parcialmente con un patrón)

-Comienza con admin
```
SELECT * FROM logins WHERE username LIKE 'admin%';
```
-Tiene 3 caracteres
```
SELECT * FROM logins WHERE username LIKE '___';
```

# OPERADORES

## AND

Evalúa a `true` solo si **ambas condiciones** son verdaderas.
se puede sustituir por &&
```
SELECT 1 = 1 AND 'test' = 'test';  -- Resultado: 1 (true)
SELECT 1 = 1 AND 'test' = 'abc';   -- Resultado: 0 (false)
```

## OR

Evalúa a `true` si **al menos una condición** es verdadera.
se puede sustituir por ||

```
SELECT 1 = 1 OR 'test' = 'abc';    -- Resultado: 1 (true)
SELECT 1 = 2 OR 'test' = 'abc';    -- Resultado: 0 (false)
```

## NOT

Invierte el valor booleano de una condición.
se puede sustituir por !=

```
SELECT NOT 1 = 1;  -- Resultado: 0 (false)
SELECT NOT 1 = 2;  -- Resultado: 1 (true)
```

## PRIORIDAD

(de mayor a menor)

- `*`, `/`, `%`
- `+`, `-`
- Comparaciones: `=`, `>`, `<`, `!=`, `LIKE`
- `NOT` (`!`)
- `AND` (`&&`)
- `OR` (`||`)