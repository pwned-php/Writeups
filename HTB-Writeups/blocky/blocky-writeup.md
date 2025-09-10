Lanzamos un nmap 


![Resultado de Nmap](Pasted%20image%2020250307182407.png)

Vamos a ver que hay por el puerto 80

Encontramos un panel de login

![Panel de Login de WordPress](Pasted%20image%2020250307182630.png)

Lanzamos un wpscan para enumerar primero usuarios

*wpscan --url http://blocky.htb/ --enumerate u, vp

![Resultado de WPScan](Pasted%20image%2020250307182915.png)

Lanzamos un gobuster y encontramos esto 

![Resultado de Gobuster](Pasted%20image%2020250307184409.png)

Encontramos en el directorio /plugins lo siguiente

Nos los descargamos y los vamos a extraer con el comando

**unzip

Nos vamos a la carpeta de /com

Con el comando

**strings BlockyCore.class

Vemos lo que hay dentro de este archivo

![Credenciales en el archivo Java](Pasted%20image%2020250307185722.png)

Parece ser unas credenciales

Nos logeamos con ellas en el panel de myadminphp y entramos


![Panel de phpMyAdmin](Pasted%20image%2020250307185759.png)

En la parte de wordpress encontramols el usuario notch y una contraseña

![Credenciales en la base de datos](Pasted%20image%2020250307190233.png)

Iniciamos sesion con ssh

Y estamos dentro

![Conexión SSH exitosa](Pasted%20image%2020250307190531.png)

Una vez dentro, ejecutamos sudo su y ponemos la misma contraseña y ya somos root
