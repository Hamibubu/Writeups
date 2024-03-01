# SoccerWalktrough
HTB

## Escaneo

`sudo nmap -v -sS -oX vulnerabilidades.xml --stylesheet="https://svn.nmap.org/nmap/docs/nmap.xsl" --script=vuln 10.10.11.194`

Encontramos 

```
22/tcp   open  ssh
80/tcp   open  http
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
|_http-csrf: Couldn't find any CSRF vulnerabilities.
| http-vuln-cve2011-3192: 
|   VULNERABLE:
|   Apache byterange filter DoS
|     State: VULNERABLE
|     IDs:  CVE:CVE-2011-3192  BID:49303
|       The Apache web server is vulnerable to a denial of service attack when numerous
|       overlapping byte ranges are requested.
|     Disclosure date: 2011-08-19
|     References:
|       https://seclists.org/fulldisclosure/2011/Aug/175
|       https://www.tenable.com/plugins/nessus/55976
|       https://www.securityfocus.com/bid/49303
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-3192
|_http-dombased-xss: Couldn't find any DOM based XSS.
9091/tcp open  xmltec-xmlmail 
```

Hay tres puertos abiertos, vamos primero al 80
Para esto agregamos la ip a /etc/hosts

Lo primero a hacer es buscar directorios

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125083930.png?raw=true)

Vemos un /tiny

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125084017.png?raw=true)

Calé SQL o NoSQL injection y nada, entonces vamos a ver si no hay alguna contraseña por default
Buscamos en su documentación https://github.com/prasathmani/tinyfilemanager

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125084222.png?raw=true)

Podemos subir un reverse shell

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125084314.png?raw=true8)

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

Lo abrimos

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125090315.png?raw=true)

Escuchamos en el puerto que pusimos

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125090343.png?raw=true)

Entramos, al entrar a la máquina veo que no se puede tomar el user así que todavía debe faltar más camino

Después de un rato de checar veo de pura casualidad el /etc/hosts

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125071829.png?raw=true)

Parece haber otro subdominio, así que lo agregamos 

Es una página similar, pero en esta nos pide logearnos, así que lo hacemos

Entramos y nos muestra una página para checar tickets, podemos tratar sql injection, interceptamos la petición con burpsuite

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125090947.png?raw=true)

Utiliza algo llamado websocket, investigando en internet parece ser que el puerto 9091 no esta abierto de casualidad es para el ws

Este solicita mediante http cambiar a websocket en una peticion GET

Podemos ver en el código fuente si utiliza ws o wss que es encriptado

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125091404.png?raw=true)

Vemos que utiliza ws, podemos intentar SQLi

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125114425.png?raw=true)
![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230125114628.png?raw=true)

Pero hay un ligero problema, si tratamos de automatizar recordemos que no estamos usando http, así que investigando encontramos un script, que genera un server en medio y detiene los pedidos y los traduce a websocket

https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html

Con esto podemos generar el server en otro puerto y eso se lo ponemos en sqlmap para pasar por ahí las cosas a ws

Primero vemos la databse en la que estamos

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124195842.png?raw=true)

Escaneamos la db encontrada

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124200742.png?raw=true)

Vemos una tabla de accounts, veamos su contenido

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124200757.png?raw=true)

Parece ser una contraseña de ssh

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124211951.png?raw=true)

Ahora parece que tenemos el user

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124214515.png?raw=true)

No se nos permite usar sudo, así que buscamos los binarios que hay con permisos -4000 y el 2>/dev/null es para ignorar los errores

Vemos que esta doas que es un binario como sudo

Investigamos los archivos de doas y vemos un .conf

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124214438.png?raw=true)

Parece que podemos correr dstat como root

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124215258.png?raw=true)

En la carpeta share se pueden crear plugins, creamos uno que nos devuelva una bash

```
import os

os.system("chmod u+s \bin\bash")
```

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124215600.png?raw=true)

Listo

![imagen](https://github.com/Hamibubu/Writeups/blob/main/SoccerWalktrough/Pasted%20image%2020230124230909.png?raw=true)





