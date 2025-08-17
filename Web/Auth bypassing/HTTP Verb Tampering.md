
Cuando una web deja diferentes metodos HTTP se puede aprovechar para mandar una request con un method rarete que no espere y poder bypassear la autorizacion.

## Bypassing basic auth

1. Identificar que directorio es el que nos pide una autorizacion http, en nuestro ejemplo es /admin
<img width="636" height="305" alt="image" src="https://github.com/user-attachments/assets/ad59dfe2-b9a1-4727-89aa-8243b7772e08" />



2.Probar otros metodos como un enfermo, en este caso nos deja usar HEAD que hace lo mismo que el GET pero no tendremos una respuesta, en este ejericico no nos deja darle al botón de reset , si lo hacemos con HEAD por mucho que el servidor no nos responda explicitamente la función reset se ejecuta de todas formas 

## Bypassing security Filters

Imaginesmos que estamos haciendo una SQLi y nos salta un filtro que dice que hemos hecho algo malicioso, puede ser que la web solo compruebe si el payload es malicioso si se hace con el metodo exacto como por ejemplo POST pero que si lo cambiamos a otro como GET el filtro no detecte nada
