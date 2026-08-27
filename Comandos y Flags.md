# Resumen de Comandos y Flags — Tasks 2 a 6

## Navegación y Conexión Remota

| Comando | Flag | Descripción |
|---|---|---|
| `ssh usuario@host` | — | Conecta a un servidor remoto por SSH. Escribe el prompt del usuario en ese host. |
| `ssh-copy-id usuario@host` | — | Copia la clave pública SSH local al `~/.ssh/authorized_keys` del host remoto, habilitando autenticación sin contraseña. Pide la contraseña una sola vez. |
| `ssh-keygen` | `-t rsa` | Genera un par de claves SSH. `-t` define el algoritmo (RSA). |
| `ssh-keygen` | `-f ~/.ssh/id_rsa` | Ruta del archivo de salida donde se guarda la clave generada. |
| `ssh-keygen` | `-N ""` | Passphrase vacía: permite autenticación 100% automática sin intervención del usuario. |
| `ssh root@stapp01` | — | Prueba de conexión como root (debe fallar con "Permission denied" después del Task 3). |

## Privilegios y Ejecución

| Comando | Flag | Descripción |
|---|---|---|
| `sudo` | — | Ejecuta el comando siguiente con privilegios de root (sin cambiar de shell). |
| `sudo su -` | — | Crea una shell como root con entorno completo (equivalente a loguearse directamente como root). |

## Gestión de Usuarios

| Comando | Flag | Descripción |
|---|---|---|
| `useradd` | `-e 2026-12-07` | Crea un nuevo usuario. `-e` define la **fecha de expiración** de la cuenta en formato YYYY-MM-DD. Sin esta flag, la cuenta nunca expira. |
| `useradd` | `-m -d /home/ammar -s /bin/bash` | (alternativa) `-m` crea home dir, `-d` especifica su ruta, `-s` define el shell de login. |
| `chage` | `-l` | Lista la información de envejecimiento/contraseña del usuario: fecha de expiración, días entre cambios, etc. |

## Gestión de Archivos y Permisos

| Comando | Flag | Descripción |
|---|---|---|
| `chmod` | `755` | Asigna permisos en modo **numérico absoluto**: owner=read+write+execute (7), grupo=read+execute (5), otros=read+execute (5). Equivale a `-rwxr-xr-x`. |
| `chmod` | `a+x` | Agrega el bit de ejecución (`x`) a **todos** (owner, grupo, otros) **sin modificar** los bits existentes. Es aditivo. |
| `chmod` | `u+x`, `g+x`, `o+x` | Agrega ejecución solo al dueño (`u`), al grupo (`g`) o a otros (`o`) respectivamente. |
| `ls` | `-l` | Lista archivos en formato largo: muestra permisos, dueño, grupo, tamaño y fecha. |
| `grep` | — | Busca patrones de texto en archivos. Usado para verificar si un usuario/variable existe en un archivo. |
| `grep ammar /etc/passwd` | — | Busca la línea del usuario `ammar` en el archivo de passwd. |
| `grep SELINUX /etc/selinux/config` | — | Verifica el valor de SELINUX en el archivo de configuración. |

## Servicios y Configuración

| Comando | Flag | Descripción |
|---|---|---|
| `vi` | — | Editor de texto de línea de comandos. Usado para editar archivos de configuración. Dentro de vi: `/texto` para buscar, `i` para insertar, `:wq` para guardar y salir. |
| `systemctl restart sshd` | — | Reinicia el servicio SSH daemon, aplicando cambios del archivo de configuración. |
| `systemctl status sshd` | — | Muestra el estado actual del servicio SSH (activo/inactivo). |
| `sshd -T` | — | Muestra la configuración **efectiva** del servidor SSH ya parseada (sin comentarios). Útil para verificar que los cambios de `sshd_config` se aplicaron. |
| `sshd -T \| grep permitrootlogin` | — | Filtra el valor de la directiva `PermitRootLogin` de la configuración SSH. |
| `sed` | `-i` | Editor de flujos de texto. `-i` edita el archivo **in-place** (sobre el mismo archivo, sin crear temporal). |
| `sed 's/^#\?Patrón.*/nuevo/'` | — | Reemplaza el patrón con el texto nuevo. `s/antiguo/nuevo/` es el formato de sustitución. |

## SELinux

| Comando | Flag | Descripción |
|---|---|---|
| `yum` | `install -y` | Instala paquetes en RHEL/CentOS. `-y` responde "sí" automáticamente a todo prompt. |
| `getenforce` | — | Muestra el estado actual de SELinux en memoria: `Enforcing`, `Permissive` o `Disabled`. Solo cambia tras reboot. |
| `sestatus` | — | Muestra estado detallado de SELinux: modo actual, config del archivo, tipo de política, etc. |
| `setenforce 1` | — | Cambia SELinux a **Enforcing** en caliente (solo hasta el próximo reboot). `setenforce 0` cambia a **Permissive**. |

## Paquetes de SELinux instalados (Task 5)

| Paquete | Descripción |
|---|---|
| `selinux-policy` | Políticas base de SELinux. |
| `selinux-policy-targeted` | Política en modo "targeted" (limita procesos específicos, no todo el sistema). |
| `libselinux` | Librería principal de SELinux para espacio de usuario. |
| `libselinux-utils` | Herramientas de línea de comandos basadas en libselinux. |
| `policycoreutils` | Utilidades básicas de administración de políticas SELinux. |
| `policycoreutils-python-utils` | (RHEL 8+) API Python para gestionar contextos y reglas SELinux. |
| `setroubleshoot-server` | Servicio que traduce logs SELinux en mensajes comprensibles. |
| `setools-console` | Herramientas de análisis y auditoría de políticas SELinux. |

---

> **Nota:** `policycoreutils-python` es el paquete para RHEL 7. En RHEL/CentOS 8+ se renombró a `policycoreutils-python-utils`.
