### Filtros

-fs por size
-fw por numero de palabras
### subdominios

```
ffuf -w /home/bonack/Descargas/bitquark-subdomains-top100000.txt -u https://10.10.156.163 -H "Host: FUZZ.support.futurevera.thm" -fs 4605
```

### Recursivo

```
ffuf -u <host> <-w wordlist> --recursion -e .php -ic
```


