# Authority

----
- Tags: #ethical #windows #activedirectory #htb 
----
Primero hacemos un escaneo de nmap 

![Pasted image 20230829225611.png]()

Podemos ver que hay gran cantidad de puertos abiertos y por la particularidad del tipo puedo asumir que se trata de un windows server 

Así que primero que nada enumeramos SMB y vemos que podemos leer dos carpetas

![Pasted image 20230829220612.png]()

Entramos por smbclient y con ello vemos una carpeta

![Pasted image 20230829220629.png]()

Después de estar checando podemos ver que en el folder de defaults vemos las credenciales en el main.yml

![Pasted image 20230829220706.png]()

![Pasted image 20230829220821.png]()

Están encriptadas en una especie de AES256 pero podemos ver que están en un formato de ansible, después de checar un rato vemos que hay una manera de pasarlos a un formato crackeable para john the reaper.

![Pasted image 20230829220900.png]()

Obtenemos el hash y lo metemos a john

![Pasted image 20230829220948.png]()
![Pasted image 20230829220927.png]()

Obtenemos una llave para desencriptar AES

![Pasted image 20230829221214.png]()

Obtenemos unas contraseñas que nos permiten como decía en el folder de smb, nos permiten entrar a una web llamada pwm que en este caso corre en el puerto 8843

![Pasted image 20230829221341.png]()

Investigando la web podemos ver que podemos editar la configuración de esta

![Pasted image 20230829221506.png]()

Con un poco más de investigación vemos que ldap está en su modo encriptado, podemos poner ldap en su modo sin encriptación para recibir contraseñas en texto plano por vía responder, así que ponemos la ip de nuestra máquina con el puerto 389

![Pasted image 20230829221542.png]()
![Pasted image 20230829221605.png]()
![Pasted image 20230829221614.png]()

Le damos a test, pero antes seteamos responder

Recibimos una contraseña en texto plano

![Pasted image 20230829221628.png]()

![Pasted image 20230829221643.png]()

Entramos vía evil-winrm

![Pasted image 20230829221745.png]()

Buscamos certificados vulnerables

![Pasted image 20230829221953.png]()
![Pasted image 20230829222006.png]()

Encontramos un certificado vulnerable

![Pasted image 20230829222223.png]()

Aregamos una computadora

![Pasted image 20230829224241.png]()

![Pasted image 20230829224403.png]()
![Pasted image 20230829224730.png]()
![Pasted image 20230829224818.png]()

Nos agregamos al grupo de admin

![Pasted image 20230829224858.png]()

Listo!