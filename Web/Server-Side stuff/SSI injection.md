Server-Side Includes, es una tecnología para crear contenido dinámico en páginas HTML. Podemos saber que usa esto normalmente gracias a las extensiones que la web emplea, como : `.shtml`, `.shtm`,  `.stm`

## SSI Directives

SSI utiliza los llamados directives para meterle contenido generado dinamicamente a una página html estática

Sintaxis :

```ssi
<!--#name param1="value1" param2="value" -->
```

EJEMPLOS: 

1. Printenv - printea environment variables
```ssi
<!--#printenv -->
```

2. Config - utilizada para cambiar la configuarción de los SSI por ejemplo para cambiar el valor de error en la web con `errmsg`
```ssi
<!--#config errmsg="Error!" -->
```

3. Echo - no hace falta explicación de lo que hace xd
-se le puede meter en la sintaxis las siguientes variables : 
- `DOCUMENT_NAME`: the current file's name
- `DOCUMENT_URI`: the current file's URI
- `LAST_MODIFIED`: timestamp of the last modification of the current file
- `DATE_LOCAL`: local server time
EJEMPO: 
```ssi
<!--#echo var="DOCUMENT_NAME" var="DATE_LOCAL" -->
```

4. Exec - ejecuta comandos
```ssi
<!--#exec cmd="whoami" -->
```

5. Include - te deja meter archivos en el directorio root de la web
```ssi
<!--#include virtual="index.html" -->
```


La explotación se basa en inyectar los directives y ver si furulan
