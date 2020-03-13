# Tunel SSH desde otra máquina


La situación es la siguiente:

* El navegador está en `client` (mi computadora personal, la cual tiene instalado el programa OpenSSH)
* En `server` hay una VM construida mediante vagrant+virtualbox corriendo que llamamos `vagrant`. (`server`puede ser la pc de tu compañero de trabajo, que tiene instalada la VM con el Jupyter Notebook.)

Queremos conectar el navegador de `client` con el Jupyter notebook de `vagrant` que corre en `server`. O sea, queremos ver en nuestro navegador, el notebook que corre en la VM de la computadora de nuestro compañero de estudio.

```bash

# En mi caso los valores de la infraestructura son los siguientes: (en el tuyo pueden variar)

# la VM Vagrant con el jupyter notebook + iracket + drracket
vagrant_ip=192.168.33.11
vagrant_port=8888
vagrant_user=vagrant

# la maquina donde corre el Virtualbox con la VM
server_ip=192.168.11.12
server_port=6666
server_user=cesarballardini

# Tu pc con el navegador
client_ip=127.0.0.1
client_port=7777


# hago un tunel desde vagrant:8888 hacia  server:6666
# y otro tunel desde server:6665 hacia client:7777

# Entonces en http://${client_ip}:${client_port} puedo ver el Jupyter notebook apuntando allí el navegador de mi pc (`client`)
 


ssh -n  -L${client_port}:127.0.0.1:${server_port} ${server_user}@${server_ip} -t ssh -L${server_port}:127.0.0.1:${vagrant_port} -i $HOME/*/.vagrant/machines/drracket/virtualbox/private_key  ${vagrant_user}@${drracket_ip} sleep 365d


```


