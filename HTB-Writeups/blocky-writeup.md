Lanzamos un nmap 


prueba/Pasted image 20250307182407.png

Vamos a ver que hay por el puerto 80

Encontramos un panel de login

![[Pasted image 20250307182630.png]]

Lanzamos un wpscan para enumerar primero usuarios

*wpscan --url http://blocky.htb/ --enumerate u, vp

![[Pasted image 20250307182915.png]]

Lanzamos un gobuster y encontramos esto 

![[Pasted image 20250307184409.png]]

Encontramos en el directorio /plugins lo siguiente

![[Pasted image 20250307185537.png]]

Nos los descargamos y los vamos a extraer con el comando

**unzip

![[Pasted image 20250307185612.png]]

Nos vamos a la carpeta de /com

Y encontramos esto

![[Pasted image 20250307185645.png]]

Con el comando

**strings BlockyCore.class

Vemos lo que hay dentro de este archivo

![[Pasted image 20250307185722.png]]

Parece ser unas credenciales

Nos logeamos con ellas en el panel de myadminphp y entramos

![[Pasted image 20250307190147.png]]

![[Pasted image 20250307185759.png]]

En la parte de wordpress encontramols el usuario notch y una contraseña


![[prueba/Pasted image 20250307190233.png]]
Iniciamos sesion con ssh

Y estamos dentro

![[Pasted image 20250307190531.png]]

Una vez dentro, ejecutamos sudo su y ponemos la misma contraseña y ya somos root

