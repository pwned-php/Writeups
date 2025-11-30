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
![Resultado de Nmap](∼/IMG/nmap.png)
