


## Plugins Attacks

```
wpscan --enumerate --url http://94.237.54.192:42355/ --api-token <token>
```

el token se consigue teniendo una cuenta en https://wpscan.com/register


## User Attacks

```shell-session
wpscan --password-attack xmlrpc -t 20 -U admin, david -P passwords.txt --url http://blog.inlanefreight.com
```



