# PhtobombWalktough

HTB machine

Primero hacemos un scaneo con nmap

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120209.png?raw=true)

Vemos que tiene el puerto 80 abierto así que modificamos etc/hosts para agregar nuestra variable para entrar con photobomb.htb

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120328.png?raw=true)

Entramos a la página web y vemos un /printer que es para el login

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120425.png?raw=true)

Vemos el código fuente

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120507.png?raw=true)

Ahí encontramos la clave para hacer el login que está en un link
Entramos y vemos que podemos hacer requests para descargar imágenes, con burpsuite vemos los parámetros

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120634.png?raw=true)

Al modificarlos arbitrariamente vemos que el servidor es ruby
Pero calamos ahora inyectando un reverse shell y lo encodeamos con url para la inyección

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120806.png?raw=true)

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120856.png?raw=true)

Escuchando en netcat con nc -lvp 5555

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107120955.png?raw=true)

Hacemos un printenv para ver las variables de entorno y hacemos también un sudo -l para ver permisos

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107121524.png?raw=true)

Vemos un directorio /opt/cleanup.sh

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107121630.png?raw=true)

	https://systemweakness.com/linux-privilege-escalation-using-path-variable-manipulation-64325ab05469 Podemos ver que se usa find, cd y cat por ahí podemos manipular para que se nos de root

Podemos manipular la variable path

Como el archivo de cleanup usa cd, find y cat y no tiene bien definidas las variables de entorno, o sea que no las usa desde /bin/cd no especifica la ruta, esto se haría para que sea más seguro

Así que nosotros podemos mover en $PATH y crear un falso cd, que haga /bin/bash, para darnos un terminal con root

`echo /bin/bash > /tmp/cd`

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221107123909.png?raw=true)


o un PWD

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PhtobombWalktough/Pasted%20image%2020221109100304.png?raw=true)

Después de crear nuestro falso cd, lo ponemos en la variable path y especificamos que use ese path para el cleanup.sh

Ponemos este path para ganar el root

Y boom tenemos root

Ya nadamás buscamos la flag

Y listo!
