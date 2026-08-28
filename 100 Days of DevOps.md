# 100 Days DevOps Challenge - Bitácora 

## Tarea 2: Create a Temporary User (ammar) on App Server 1

### Requerimiento

As part of the temporary assignment to the Nautilus project, a developer named ammar requires access for a limited duration. To ensure smooth access management, a temporary user account with an expiry date is needed. Here's what you need to do:

- Create a user named `ammar` on App Server 1 in Stratos Datacenter.
- Set the expiry date to `2026-12-07`, ensuring the user is created in lowercase as per standard protocol.

**Note:** You can find the infrastructure details by clicking on the "Details of all Users and Servers" button on the top-right section of the page.

### Resolución

```bash
# 1. Acceder por SSH a App Server 1 (según datos del lab, ej.)
ssh tony@stapp01

# 2. Bajar privilegio a root (o usar sudo en cada comando)
sudo su -

# 3. Crear el usuario ammar con fecha de expiración
useradd -e 2026-12-07 ammar

# 4. Verificar que el usuario existe y su expiración
grep ammar /etc/passwd        # confirmar que el nombre está en minúsculas
chage -l ammar                # mostrar detalle de caducidad (Account expires: Dec 07, 2026)
```

**Alternativa con sudo (sin hacer `su -`):**
```bash
sudo useradd -e 2026-12-07 ammar
sudo chage -l ammar
```

### Notas / Troubleshooting

- La opción `-e YYYY-MM-DD` define la fecha de expiración de la cuenta en formato `cambio de fechas` del sistema (formato ISO).
- Si se necesita carpeta home, se puede agregar `-m -d /home/ammar -s /bin/bash`.
- Verificar siempre con `chage -l ammar` que la línea `Account expires` muestre la fecha pedida.
- Los datos de conexión (usuario y servidor) están en el botón "Details of all Users and Servers".

## Tarea 3: Disable Direct SSH Root Login on All App Servers

### Requerimiento

Following security audits, the xFusionCorp Industries security team has rolled out new protocols, including the restriction of direct root SSH login.

Your task is to disable direct SSH root login on all app servers within the Stratos Datacenter.

**Note:** You can find the infrastructure details by clicking on the "Details of all Users and Servers" button on the top-right section of the page.

### Resolución

```bash
# Repetir en CADA app server: stapp01 (tony), stapp02 (steve), stapp03 (banner)

# 1. Acceder por SSH al app server
ssh tony@stapp01

# 2. Bajar privilegio a root
sudo su -

# 3. Editar la configuración del servidor SSH
vi /etc/ssh/sshd_config

#    Buscar la línea PermitRootLogin (puede estar comentada con #):
#      /PermitRootLogin        (buscar en vi)
#    Dejarla así (sin # y con valor no):
#      PermitRootLogin no

# 4. Reiniciar el servicio SSH para aplicar el cambio
systemctl restart sshd

# 5. Verificar que el cambio quedó aplicado
sshd -T | grep permitrootlogin     # debe mostrar: permitrootlogin no

# 6. Probar desde tu máquina que root ya NO puede loguearse
ssh root@stapp01                   # debe fallar con "Permission denied"
```

**Alternativa sin editor (agregar/reemplazar la directiva directamente):**
```bash
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd
```

### Notas / Troubleshooting

- `PermitRootLogin` puede tener valores: `yes` (permitido), `no` (bloqueado), `prohibit-password` (solo bloquea login con contraseña, permite con key).
- No cerrar la sesión actual hasta confirmar que SSH sigue funcionando; si rompés la config podrías quedarte fuera del servidor.
- En algunos sistemas el servicio se llama `sshd`, en otros `ssh`. Verificar con `systemctl status sshd`.
- `sshd -T` muestra la configuración efectiva ya parseada (útil para validar sin reiniciar dos veces).
- Recordar repetir el procedimiento en los **3 servidores**: stapp01, stapp02 y stapp03.

## Tarea 4: Grant Execute Permissions to xfusioncorp.sh

### Requerimiento

In a bid to automate backup processes, the xFusionCorp Industries sysadmin team has developed a new bash script named xfusioncorp.sh. While the script has been distributed to all necessary servers, it lacks executable permissions on one of the app servers within the Stratos Datacenter.

Your task is to grant executable permissions to the `/tmp/xfusioncorp.sh` script on that app server. Additionally, ensure that all users have the capability to execute it.

**Note:** You can find the infrastructure details by clicking on the "Details of all Users and Servers" button on the top-right section of the page.

> **Ojo:** el servidor objetivo varía según el intento del lab. Ya nos tocó App Server 3 (`stapp03`, user `banner`) y App Server 1 (`stapp01`, user `tony`). Fijarse bien cuál pide la consigna.

### Resolución

```bash
# 1. Acceder por SSH al app server indicado en la consigna
ssh banner@stapp03        # o: ssh tony@stapp01

# 2. Bajar privilegio a root (o usar sudo en cada comando)
sudo su -

# 3. Dar permisos de ejecución a todos los usuarios (dueño, grupo y otros)
chmod 755 /tmp/xfusioncorp.sh # ya que el estado inicial del .sh en cuanto a permisos era: ---------- y necesitaba como resultado -rwxr-x-r-x

# 4. Verificar los permisos
ls -l /tmp/xfusioncorp.sh     # debe mostrar algo como: -rwxr-xr-x ... root root
```

### Notas / Troubleshooting

- `chmod a+x` agrega el bit de ejecución a **todos** (`u`=dueño, `g`=grupo, `o`=otros; `a` = todos a la vez). Equivale a `chmod +x`.
- Alternativa numérica: `chmod 755 /tmp/xfusioncorp.sh` (rwxr-xr-x), que es lo típico para scripts ejecutables por todos.
- Si el archivo fuera de otro dueño y quisieras restringir, se usaría solo `u+x`, pero acá piden explícitamente que **todos** puedan ejecutarlo.
- **Cuidado con `---x--x--x` (chmod 111):** aunque tiene la `x` para todos, los *scripts* también necesitan permiso de **lectura** para que bash pueda abrirlos e interpretarlos (un binario corre solo con `--x`, un script no → `Permission denied`). El estado final esperado es `-rwxr-xr-x`.
- `a+x` es aditivo: solo agrega la `x` y conserva el resto de los bits. Desde el estado original `-rw-r--r--` (644) da justo 755, pero si el archivo ya estaba en `---x--x--x` (sin lectura), `+x` no recupera la `r`.
- Por eso conviene usar modo absoluto `chmod 755`: fija los permisos finales sin importar el estado previo. Probado y funcionando para todos los usuarios.
- Validar ejecutándolo como un usuario común: `/tmp/xfusioncorp.sh --help` o simplemente verificar con `ls -l` que las tres posiciones de ejecución estén en `x`.

## Tarea 5: Install SELinux Packages and Disable SELinux on App Server 2

### Requerimiento

Following a security audit, the xFusionCorp Industries security team has opted to enhance application and server security with SELinux. To initiate testing, the following requirements have been established for App Server 2 in the Stratos Datacenter:

- Install the required SELinux packages.
- Permanently disable SELinux for the time being; it will be re-enabled after necessary configuration changes.
- No need to reboot the server, as a scheduled maintenance reboot is already planned for tonight.
- Disregard the current status of SELinux via the command line; the final status after the reboot should be disabled.

**Note:** You can find the infrastructure details by clicking on the "Details of all Users and Servers" button on the top-right section of the page.

### Resolución

```bash
# 1. Acceder por SSH a App Server 2
ssh steve@stapp02

# 2. Bajar privilegio a root
sudo su -

# 3. Instalar los paquetes de SELinux
yum install -y selinux-policy selinux-policy-targeted \
               libselinux libselinux-utils \
               policycoreutils policycoreutils-python \
               setroubleshoot-server setools-console

# 4. Deshabilitar SELinux permanentemente editando la config
vi /etc/selinux/config

#    Dejar la línea así:
#      SELINUX=disabled
#    (las otras líneas quedan: SELINUXTYPE=targeted)

# 5. Verificar que quedó guardado el cambio
grep SELINUX /etc/selinux/config     # debe mostrar: SELINUX=disabled
```

### Notas / Troubleshooting

- La consigna dice explícitamente que **no hay que reiniciar** y que se ignore lo que muestre `getenforce` o `sestatus`: esos comandos reflejan el estado *actual en memoria*, que solo cambia tras el reboot. Lo que valida el lab es el archivo `/etc/selinux/config`.
- Valores posibles de `SELINUX=` en `/etc/selinux/config`:
  - **`enforcing`** — SELinux activo y **aplicando** las políticas: cualquier acceso no autorizado por la política se **bloquea** además de loguearse (en `/var/log/audit/audit.log`). Es el modo por defecto en RHEL/CentOS.
  - **`permissive`** — SELinux está cargado pero en modo "solo observación": las violaciones a la política **no se bloquean**, solo se **registran** en los logs. Útil para diagnosticar qué reglas faltan antes de activarlo (se pueden convertir esos logs en reglas con `audit2allow`).
  - **`disabled`** — SELinux completamente **apagado**: el kernel no carga ninguna política, no hay bloqueos ni registros SELinux, y solo rigen los permisos tradicionales de UNIX (DAC). Acá pide este valor.
  - Regla práctica para cambiar entre estados: entre `enforcing` ↔ `permissive` se puede cambiar **en caliente** con `setenforce 1|0`; para entrar o salir de `disabled` siempre hace falta editar la config + **reboot**, porque el kernel carga/descarga todo el subsistema al arrancar.

## Tarea 6: Set Up Password-less SSH from Jump Host to All App Servers

### Requerimiento

The system admins team of xFusionCorp Industries has set up some scripts on jump host that run on regular intervals and perform operations on all app servers in Stratos Datacenter. To make these scripts work properly we need to make sure the `thor` user on jump host has password-less SSH access to all app servers through their respective sudo users (i.e. tony for app server 1).

Based on the requirements, perform the following:
- Set up password-less authentication from user `thor` on jump host to all app servers through their respective sudo users.

**Note:** You can find the infrastructure details by clicking on the "Details of all Users and Servers" button on the top-right section of the page.

### Resolución

```bash
# El desafío ya te deja logueado como thor en el jump host, no hace falta conectarte a él

# 1. Generar par de claves SSH (si no existe ya)
ssh-keygen -t rsa -f ~/.ssh/id_rsa -N ""
#  — -N "" = sin passphrase, para que sea 100% automático

# 2. Copiar la clave pública a cada app server (pedirá la contraseña UNA vez)
ssh-copy-id tony@stapp01    # password: Ir0nM@n
ssh-copy-id steve@stapp02   # password: Am3ric@
ssh-copy-id banner@stapp03  # password: BigGr33n

# 3. Probar que cada conexión funciona sin contraseña
ssh tony@stapp01             # debe entrar sin pedir password
ssh steve@stapp02
ssh banner@stapp03
```

### Notas / Troubleshooting

- `ssh-copy-id` agrega la clave pública a `~/.ssh/authorized_keys` del servidor destino. Puedes verificarlo manualmente si lo necesitás: `cat ~/.ssh/authorized_keys` en el app server.
- Si `ssh-keygen` dice que `id_rsa` ya existe, preguntará si quieres sobrescribir: `n` si ya tenías claves que usás, `s` si quieres empezar de cero.
- `id_rsa` = clave privada, **jamás** compartirla. `id_rsa.pub` = clave pública, esa es la que se copia con `ssh-copy-id`.
- `thor` es el usuario del **jump host**; `tony`, `steve`, `banner` son los usuarios con sudo en cada app server. La conexión va de thor → usuario del app server.
- No confundir con `setenforce 0` (pasa a permissive solo hasta el próximo reboot, no modifica el archivo) ni con `SELINUX=permissive`.
- Si `yum install` dice "already installed", no pasa nada: los paquetes ya estaban, seguís igual.
- **Ojo con `policycoreutils-python`:** en RHEL/CentOS 8+ el paquete se renombró a `policycoreutils-python-utils` (si yum dice "unable to find", es esa la razón). Verificar versión del SO con `cat /etc/os-release`.
- Para re-habilitar más adelante: `SELINUX=enforcing` en la config + reboot. Ojo: al re-habilitar puede tardar en relabelar el filesystem.

## Tarea 7: Install Ansible 4.8.0 on Jump Host (globally)

### Requerimiento

During the weekly meeting, the Nautilus DevOps team discussed about the automation and configuration management solutions that they want to implement. While considering several options, the team has decided to go with Ansible for now due to its simple setup and minimal pre-requisites. The team wanted to start testing using Ansible, so they have decided to use jump host as an Ansible controller to test different kind of tasks on rest of the servers.

Your task:
- Install `ansible` version **4.8.0** on Jump host using **pip3 only**.
- Make sure the Ansible binary is available **globally** on this system, i.e. all users on this system are able to run Ansible commands.

**Note:** You can find the infrastructure details by clicking on the "Details of all Users and Servers" button on the top-right section of the page.

### Resolución

```bash
# El desafío ya te deja logueado como thor en el jump host

# 1. Verificar si pip3 está disponible (o instalarlo primero)
pip3 --version          # si no existe: yum install -y python3-pip

# 2. Bajar a root para instalar de forma GLOBAL (no solo para thor)
sudo su -

# 3. Instalar ansible 4.8.0 con pip3 (la versión exacta con ==)
pip3 install ansible==4.8.0

# 4. Verificar la instalación
ansible --version       # debe mostrar "ansible [core ...]" con la versión base y Python

# 5. Probar que CUALQUIER usuario lo puede ejecutar (salir de root y probar con thor, etc.)
exit
ansible --version       # sigue funcionando
```

**Si pip3 no está instalado (instalar primero como root):**
```bash
yum install -y python3-pip
```

### Notas / Troubleshooting

- **Importante:** la versión exacta se fija con `pip3 install ansible==4.8.0` (doble `=`). Sin `==`, pip3 instalaría la última disponible.
- Para que sea **global**, hay que instalar como **root**; si se instala con `pip3 install --user`, el binario queda solo en `~/.local/bin` del usuario actual y nadie más podría correrlo.
- La versión *core* que trae ansible 4.8.0 internamente será algo como `ansible-core 2.11.x` (ej. `ansible [core 2.11.14]`). Eso es normal, la versión de colección es la 4.8.0.
- Documentación del comando: `pip install <paquete>` instala un paquete Python; `==version` pin de versión exáctica.
- Si pip3 tira error de versión de Python (ansible 4 requiere Python >= 3.8), verificar con `python3 --version`; en jump hosts muy viejos (CentOS 7 / py3.6) habría que revisar la distro.