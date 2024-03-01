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

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/431fe77b-bd27-436a-8f4d-2b2cc8b327fe)

Lo primero a hacer es buscar directorios

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/eed61078-516b-49b2-b0fa-3bf2828d3ad2)

Vemos un /tiny

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/c13abc98-fe6f-44de-9cab-8a4f7158fe60)

Calé SQL o NoSQL injection y nada, entonces vamos a ver si no hay alguna contraseña por default
Buscamos en su documentación https://github.com/prasathmani/tinyfilemanager

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/2b73b435-f6fc-4472-bb83-dfe5db899a58)

Podemos subir un reverse shell

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/8975123d-e3fc-4675-ab65-968a459d88f8)

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

Lo abrimos

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/c8791f85-f7af-4b5d-9297-28a090322648)

Escuchamos en el puerto que pusimos

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/01f871a0-4a11-4e7b-a6a6-bcc12c47b602)

Entramos, al entrar a la máquina veo que no se puede tomar el user así que todavía debe faltar más camino

Después de un rato de checar veo de pura casualidad el /etc/hosts

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/040007b6-99fe-4f83-91c6-2cdc52d6f4e0)

Parece haber otro subdominio, así que lo agregamos 

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/62f8dd2a-09a8-41e4-aa4b-3078b57598de)

Es una página similar, pero en esta nos pide logearnos, así que lo hacemos

Entramos y nos muestra una página para checar tickets, podemos tratar sql injection, interceptamos la petición con burpsuite

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/864a2227-794e-43c4-964e-6b576a148eaf)

Utiliza algo llamado websocket, investigando en internet parece ser que el puerto 9091 no esta abierto de casualidad es para el ws

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/453f9bf9-d03d-465b-96cb-0bf9d474f09e)

Este solicita mediante http cambiar a websocket en una peticion GET

Podemos ver en el código fuente si utiliza ws o wss que es encriptado

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/1f6b36cc-121d-4120-b643-bad2bda5d3ac)

Vemos que utiliza ws, podemos intentar SQLi

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/1ce71468-e98c-4ae8-b0d8-d7734c178877)
![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/52cafa5e-a1c0-45bb-9e65-43400f6bcb7c)

Pero hay un ligero problema, si tratamos de automatizar recordemos que no estamos usando http, así que investigando encontramos un script, que genera un server en medio y detiene los pedidos y los traduce a websocket

https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html

Con esto podemos generar el server en otro puerto y eso se lo ponemos en sqlmap para pasar por ahí las cosas a ws

Primero vemos la databse en la que estamos

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/5b928c76-2658-4525-93d0-c691c74c0610)

Escaneamos la db encontrada

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/bba55dca-58de-446b-9630-9a5c3b2da897)

Vemos una tabla de accounts, veamos su contenido

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/587cd747-57e6-4951-b0f2-a602738502db)

Parece ser una contraseña de ssh

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/eb298f8d-fdc9-4402-9212-9ae87c7093f5)

Ahora parece que tenemos el user

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/f744eef3-a777-4fa7-be0c-6d762319afa3)

No se nos permite usar sudo, así que buscamos los binarios que hay con permisos -4000 y el 2>/dev/null es para ignorar los errores

Vemos que esta doas que es un binario como sudo

Investigamos los archivos de doas y vemos un .conf

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/734df9b0-2fb9-4a5f-b647-a607adc1cc97)

Parece que podemos correr dstat como root

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/4b7b10a6-f09d-4c98-8d72-2e4da654a029)

En la carpeta share se pueden crear plugins, creamos uno que nos devuelva una bash

```
import os

os.system("chmod u+s \bin\bash")
```

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/74b3c0f8-f72a-49be-b313-c4b7aff1a58b)

Listo

![imagen](https://github.com/Hamibubu/SoccerWalktrough/assets/108554878/12df32f6-d9ac-4316-bb87-b3596491d3ea)





