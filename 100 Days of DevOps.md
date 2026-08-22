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