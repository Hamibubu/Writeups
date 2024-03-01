# Authority

----
- Tags: #ethical #windows #activedirectory #htb 
----
Primero hacemos un escaneo de nmap 

![Pasted image 20230829225611.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829225611.png?raw=true)

Podemos ver que hay gran cantidad de puertos abiertos y por la particularidad del tipo puedo asumir que se trata de un windows server 

Así que primero que nada enumeramos SMB y vemos que podemos leer dos carpetas

![Pasted image 20230829220612.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829220612.png?raw=true)

Entramos por smbclient y con ello vemos una carpeta

![Pasted image 20230829220629.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829220629.png?raw=true)

Después de estar checando podemos ver que en el folder de defaults vemos las credenciales en el main.yml

![Pasted image 20230829220706.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829220706.png?raw=true)

![Pasted image 20230829220821.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829220821.png?raw=true)

Están encriptadas en una especie de AES256 pero podemos ver que están en un formato de ansible, después de checar un rato vemos que hay una manera de pasarlos a un formato crackeable para john the reaper.

![Pasted image 20230829220900.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829220900.png?raw=true)

Obtenemos el hash y lo metemos a john

![Pasted image 20230829220948.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829220927.png?raw=true)
![Pasted image 20230829220927.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829220948.png?raw=true)

Obtenemos una llave para desencriptar AES

![Pasted image 20230829221214.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221214.png?raw=true)

Obtenemos unas contraseñas que nos permiten como decía en el folder de smb, nos permiten entrar a una web llamada pwm que en este caso corre en el puerto 8843

![Pasted image 20230829221341.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221341.png?raw=true)

Investigando la web podemos ver que podemos editar la configuración de esta

![Pasted image 20230829221506.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221506.png?raw=true)

Con un poco más de investigación vemos que ldap está en su modo encriptado, podemos poner ldap en su modo sin encriptación para recibir contraseñas en texto plano por vía responder, así que ponemos la ip de nuestra máquina con el puerto 389

![Pasted image 20230829221542.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221605.png?raw=true)
![Pasted image 20230829221605.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221614.png?raw=true)
![Pasted image 20230829221614.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221643.png?raw=true)

Le damos a test, pero antes seteamos responder

Recibimos una contraseña en texto plano

![Pasted image 20230829221628.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221628.png?raw=true)

Entramos vía evil-winrm

![Pasted image 20230829221745.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221745.png?raw=true)

Buscamos certificados vulnerables

![Pasted image 20230829221953.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829221953.png?raw=true)
![Pasted image 20230829222006.png]()

Encontramos un certificado vulnerable

![Pasted image 20230829222223.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829222129.png?raw=true)

Aregamos una computadora

![Pasted image 20230829224241.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829224241.png?raw=true)

![Pasted image 20230829224403.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829224403.png?raw=true)
![Pasted image 20230829224730.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829224730.png?raw=true)
![Pasted image 20230829224818.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829224818.png?raw=true)

Nos agregamos al grupo de admin

![Pasted image 20230829224858.png](https://github.com/Hamibubu/Writeups/blob/main/Authority/Pasted%20image%2020230829224858.png?raw=true)

Listo!
