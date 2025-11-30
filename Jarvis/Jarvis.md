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
