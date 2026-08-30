# Curso Linux Level 1 - Bitácora de Tareas

Bitácora de resolución de los tasks del curso **Linux Level 1** de KodeKloud, siguiendo el mismo formato que el resto del repositorio.

---

## 📝 Formato de cada Tarea

1. **Requerimiento:** Contexto del escenario / ticket del lab y condiciones de éxito.
2. **Resolución Paso a Paso:** Comandos detallados y comentados.
3. **Alternativas de comando:** Enfoques directos o automatizados (ej. `sed` en vez de edición manual).
4. **Notas / Troubleshooting & Verificación:** Comprobaciones de validación y puntos clave.
5. **Glosario de comandos y flags:** Explicación de cada flag utilizado.

---

## Tarea 2: Create Group nautilus_developers and Add User kano

### Requerimiento

The system admin team at xFusionCorp Industries has streamlined access management by implementing group-based access control. Here's what you need to do:

- **a.** Create a group named `nautilus_developers` across all App servers within the Stratos Datacenter.
- **b.** Add the user `kano` into the `nautilus_developers` group on all App servers. If the user doesn't exist, create it as well.

**Note:** You can find the infrastructure details by clicking on the "Details of all Users and Servers" button on the top-right section of the page.

### Resolución

```bash
# Repetir en CADA app server: stapp01 (tony), stapp02 (steve), stapp03 (banner)

# 1. Acceder por SSH al app server
ssh tony@stapp01        # luego: ssh steve@stapp02, ssh banner@stapp03

# 2. Bajar privilegio a root (o usar sudo en cada comando)
sudo su -

# 3. Crear el grupo (si ya existe, groupadd avisa y no rompe nada)
groupadd nautilus_developers

# 4. Confirmar que el usuario kano existe; si no, crearlo
id kano                  # si no existe -> "no such user"
useradd kano             # solo si el paso anterior dio "no such user"

# 5. Agregar al usuario al grupo (append, para no sacarlo de otros grupos)
usermod -a -G nautilus_developers kano

# 6. Verificar
getent group nautilus_developers   # debe listar a kano como miembro
groups kano                        # debe incluir nautilus_developers
id kano                            # muestra gid del grupo + grupos secundarios
```

### Alternativas

```bash
# Todo en un solo comando con sudo (sin hacer su -):
sudo groupadd nautilus_developers
sudo useradd kano                              # solo si hace falta
sudo usermod -aG nautilus_developers kano
sudo getent group nautilus_developers

# Si quieren crear el usuario y agregarlo al grupo en UNA sola llamada:
useradd -G nautilus_developers kano            # solo si el user NO existe todavía
```

### Notas / Troubleshooting

- **`-a` (append) es obligatorio** junto a `-G` cuando el usuario ya existe: sin `-a`, `usermod -G grupo usuario` **reemplaza** todos los grupos secundarios del usuario por el indicado (peligro de sacarlo de grupos donde ya estaba). Con usuario recién creado no importa, pero es buena práctica usarlo siempre.
- `groupadd` falla con "already exists" si el grupo ya está creado; en ese caso simplemente seguir con el paso siguiente (no pasa nada).
- `getent group nautilus_developers` muestra `nautilus_developers:x:GID:` y, si `kano` es único miembro de ese grupo, su nombre aparece como tercer miembro principal. `groups kano` es la forma más directa de validar la membresía.
- El usuario se crea **en minúsculas** (`kano`) aunque el enunciado lo muestre tal cual; los nombres de usuario en Linux no distinguen mayúsculas de forma estándar, pero se respeta la convención del lab.
- Recordar repetir en los **3 servidores**: stapp01, stapp02 y stapp03. El lab valida grupo + usuario + membresía en cada uno de ellos.

---

## Glosario de comandos y flags (en construcción)

| Comando | Flag | Descripción |
|---|---|---|
| `groupadd` | | Crea un grupo nuevo. Si ya existe: "already exists" (no rompe). |
| `useradd` | | Crea un usuario nuevo (sin home si no se pasa `-m`; en el lab basta con que exista). |
| `usermod` | `-a` | Append: agrega grupos **sin reemplazar** los existentes (obligatorio con `-G`). |
| `usermod` | `-G` | Asigna grupos **secundarios** (secundary groups). Sin `-a` reemplaza todos. |
| `getent` | | Consulta bases de datos del sistema (passwd, group, …). `getent group X` muestra GID y miembros. |
| `groups` | | Lista los grupos del usuario. `groups <user>` para otro usuario. |
| `id` | | Muestra UID, GID principal y grupos secundarios del usuario actual (o del indicado). |