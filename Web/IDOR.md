

practicar mas este módulo que htb iba mal y use la cheet shit xd

/index.php?id=X -> cuando se accede mediante un id o algo asi a un archivo por ejemplo , si nosotros podemos modificar el id y acceder a archivos de otras personas eso es un IDOR, tambien se puede dar que el id este encodeado de alguna forma pero es mas de lo mismo

-Si por ejemplo tenemos el codigo fuente de una web y sabemos que el id de un objeto se hace con el numero encodeado en base 64 y luego en hash md5 podremos hacer una lista con todos los ids encodeados de esa forma : 

```shell-session
bonack@htb[/htb]$ for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done

cdd96d3cc73d1dbdaffa03cc6cd7339b
0b7e7dee87b1c3b98e72131173dfbbbf
0b24df25fe628797b3a50ae0724d2730
f7947d50da7a043693a592b4db43b0a1
8b9af1f7f76daf0f02bd9c48c4a2e3d0
006d1236aee3f92b8322299796ba1989
b523ff8d1ced96cef9c86492e790c2fb
d477819d240e7d3dd9499ed8d23e7158
3e57e65a34ffcb2e93cb545d024f5bde
5d4aace023dc088767b4e08c79415dcd
```

-Otro caso es que no nos deje cambiar o descargar archivos de otros usuarios porque el id sea dificil de fuzzear entonces lo podremos combinar con HTTP verb tampering para ver si podemos hacer Get a otros usuarios conseguir su id que no podemos descifrar y de ahí explotar IDORS


