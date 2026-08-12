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