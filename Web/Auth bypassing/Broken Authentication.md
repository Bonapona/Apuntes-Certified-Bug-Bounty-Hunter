
## Authentication Bypass via Direct Access

Si por ejemplo no podemos acceder a /admin.php en una web , si la web está mal configurada, y aunque nos haga redirect el contenido de la página se renderiza, podemos bypassear el redirect interceptando la petición con burpsuite y luego cambiandole la respuesta de 302 a 200 
