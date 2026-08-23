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