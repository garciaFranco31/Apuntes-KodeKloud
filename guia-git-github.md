# Guía de Git y GitHub 


Guía práctica para aprender los conceptos y comandos fundamentales de Git (control de versiones) y GitHub (plataforma para alojar repositorios).

---

## 1. ¿Qué es Git y qué es GitHub?

- **Git** es un sistema de **control de versiones** distribuido. Permite llevar un historial de todos los cambios realizados sobre un proyecto, trabajar en equipo y volver atrás cuando sea necesario.
- **GitHub** es una **plataforma web** que aloja repositorios de Git en la nube, permitiendo almacenar, compartir y colaborar en proyectos.

> Git lo usas en tu computadora (local). GitHub es el sitio remoto donde subes tu código (remoto).

---

## 2. Instalación y primeras configuraciones

### Instalar Git

- **Windows**: descargar el instalador desde https://git-scm.com/downloads
- **macOS**: `brew install git`
- **Linux (Debian/Ubuntu)**: `sudo apt install git`

Verificar la instalación:

```bash
git --version
```

### Configurar usuario (obligatorio, se hace una sola vez)

Git necesita saber quién hace cada cambio. Los datos quedan guardados en un archivo de configuración global.

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
```

Verificar la configuración:

```bash
git config --list
```

Otras configuraciones útiles:

```bash
git config --global core.editor "code --wait"   # editor por defecto
git config --global init.defaultBranch main     # rama inicial como "main"
git config --global color.ui auto               # colores en la salida
```

---

## 3. Crear un repositorio en GitHub

1. Iniciar sesión en https://github.com y hacer clic en el botón **New** (o **+** → **New repository**).
2. Elegir un nombre para el repositorio (ej. `mi-proyecto`).
3. Opciones:
   - **Public** (todo el mundo lo ve) o **Private** (solo tú y quienes invites).
   - **Add a README file**: crea un archivo de descripción (recomendado).
   - **.gitignore**: archivo que le dice a Git qué archivos ignorar (recomendado, elegir la plantilla según el lenguaje).
   - **License**: licencia del proyecto (opcional).
4. Hacer clic en **Create repository**.

La URL del repositorio tendrá una forma similar a:

```
https://github.com/usuario/mi-proyecto.git
```

---

## 4. Inicializar Git en un proyecto local

### Opción A: Subir un proyecto que ya existe en tu PC

Navegar hasta la carpeta del proyecto:

```bash
cd ruta/del/proyecto
```

Inicializar un repositorio de Git dentro de la carpeta:

```bash
git init
```

Esto crea una carpeta oculta `.git` que contiene todo el historial. Conectar el repositorio local con el remoto de GitHub:

```bash
git remote add origin https://github.com/usuario/mi-proyecto.git
```

Ver los remotos configurados:

```bash
git remote -v
```

### Opción B: Clonar un repositorio existente (bajarlo por primera vez)

Si el repositorio ya existe en GitHub y quieres una copia local:

```bash
git clone https://github.com/usuario/mi-proyecto.git
```

Esto crea una carpeta `mi-proyecto` con todo el historial y ya conectada al remoto (no hace falta `git init` ni `git remote add`).

---

## 5. Flujo básico de trabajo con Git

### Conceptos clave: los tres estados

1. **Working Directory**: los archivos que modificas en tu editor.
2. **Staging Area (index)**: los archivos que preparas para el commit.
3. **Repository (local)**: los cambios que ya fueron guardados (commits).

### Diagrama del flujo

```
Archivos modificados  →  git add  →  Área de staging  →  git commit  →  Repositorio local
```

### Ver el estado del repositorio

```bash
git status
```

### Agregar archivos al staging area

```bash
git add archivo.txt            # un solo archivo
git add src/                   # toda una carpeta
git add .                      # todos los archivos modificados/creados
```

### Guardar los cambios (commit)

```bash
git commit -m "Mensaje descriptivo del cambio"
```

> Consejos para buenos mensajes de commit:
> - Imperativo y breve: "Agregar función de login", "Corregir error en cálculo de IVA".
> - Explicar el **por qué**, no el qué.

### Ver el historial de commits

```bash
git log
git log --oneline             # más compacto
git log --oneline --graph     # muestra las ramas de forma gráfica
```

---

## 6. Subir cambios al repositorio remoto (push)

Cuando ya tienes un commit local, lo envías a GitHub:

```bash
git push
```

La primera vez de una rama nueva (o para mayor claridad):

```bash
git push -u origin main
```

- `-u` (o `--set-upstream`) crea la relación entre la rama local `main` y la remota `origin/main`, para que luego baste con `git push`.

Verificar que todo esté sincronizado:

```bash
git status
git log --oneline origin/main
```

---

## 7. Bajar cambios del repositorio remoto

### git pull (trae y fusiona los cambios)

```bash
git pull
```

Hace dos cosas a la vez: `git fetch` (descarga los cambios) + `git merge` (los incorpora a tu rama).

### git fetch (solo descarga, no modifica tu trabajo)

```bash
git fetch
git diff origin/main          # ver diferencias sin fusionarlas
```

### Forma clara de traer cambios de una rama específica:

```bash
git pull origin main
```

---

## 8. Trabajo con ramas (branches)

Una **rama** es una línea independiente de desarrollo. Te permite trabajar en una funcionalidad nueva sin afectar la rama principal (`main`).

### Rama principal

```bash
git branch                    # lista las ramas locales (la actual tiene *)
git branch -a                 # lista también las remotas
```

### Crear una rama nueva

```bash
git branch nombre-rama
```

### Crear y moverse a ella al mismo tiempo

```bash
git checkout -b nombre-rama
# o bien
git switch -c nombre-rama
```

### Cambiar de rama

```bash
git checkout nombre-rama
# o bien
git switch nombre-rama
```

> IMPORTANTE: antes de cambiar de rama, asegurarte de haber hecho `commit` (o `git stash`) de tus cambios sin guardar, para no perderlos.

### Subir la rama nueva al remoto

```bash
git push -u origin nombre-rama
```

### Eliminar ramas

```bash
git branch -d nombre-rama         # local (solo si ya fue fusionada)
git branch -D nombre-rama         # forzar eliminación
git push origin --delete nombre-rama   # remota
```

### Ejemplo del flujo con ramas

```bash
git checkout -b feature-login          # creas y entras a la rama
# ... haces cambios en el código ...
git add .
git commit -m "Agregar pantalla de login"
git push -u origin feature-login       # la subes a GitHub
```

---

## 9. Fusionar ramas (merge)

El **merge** junta los cambios de una rama con otra. Normalmente queremos integrar una rama de trabajo dentro de `main`.

### Pasos para fusionar

1. Moverse a la rama que **recibe** los cambios (normalmente `main`):

```bash
git checkout main
```

2. Asegurarse de tener la última versión:

```bash
git pull
```

3. Fusionar la rama de trabajo:

```bash
git merge feature-login
```

4. Subir el resultado:

```bash
git push
```

### Fast-forward vs merge normal

- **Fast-forward**: si `main` no tuvo nuevos commits, Git simplemente avanza la línea. No genera commit extra.
- **Merge commit**: si ambas ramas avanzaron, Git crea un commit de fusión con dos padres.

### Conflictos de merge

Si dos personas (o un mismo proyecto) cambian las mismas líneas, Git no puede fusionar solo y marca un **conflicto**:

- Salida similar a: `CONFLICT (content): Merge conflict in archivo.txt`
- El archivo contendrá marcadores:

```
<<<<<<< HEAD
tu versión
=======
versión de la otra rama
>>>>>>> feature-login
```

Para resolverlo:

1. Abrir el archivo en el editor.
2. Elegir qué líneas quedan (una, la otra o ambas), eliminando los marcadores `<<<<<<<`, `=======` y `>>>>>>>`.
3. Guardar y marcar como resuelto:

```bash
git add archivo.txt
```

4. Finalizar el merge:

```bash
git commit -m "Resolver conflicto en archivo.txt"
```

### Abortar un merge en caso de problemas

```bash
git merge --abort
```

---

## 10. Comandos útiles en el camino

| Comando | Para qué sirve |
|---|---|
| `git status` | Ver el estado del repositorio |
| `git diff` | Ver los cambios aún no agregados |
| `git diff --staged` | Ver los cambios ya en staging |
| `git log --oneline` | Historial de commits compacto |
| `git stash` | Guardar temporalmente cambios sin commitear |
| `git stash pop` | Recuperar los cambios guardados |
| `git reset HEAD archivo` | Sacar un archivo del staging area |
| `git reset --hard HEAD` | Descartar cambios locales sin commitear (¡cuidado!) |
| `git branch -a` | Listar todas las ramas (locales y remotas) |
| `git remote -v` | Ver los repositorios remotos configurados |
| `git push origin rama` | Subir cambios de una rama al remoto |
| `git pull origin rama` | Bajar y fusionar cambios de una rama remota |
| `git clone <url>` | Copiar un repositorio remoto a local |

---

## 11. Buenas prácticas

1. **Hacer commits pequeños y frecuentes**, con mensajes descriptivos.
2. **No subir archivos sensibles** (contraseñas, claves API, tokens). Usar `.gitignore`.
3. **Hacer `pull` antes de `push`** para evitar conflictos en equipos.
4. **Trabajar con ramas** para cada funcionalidad nueva; no commitear directo en `main`.
5. **Commitiar después de cada cambio lógico**, no al final de todo.
6. **Revisar `git status` y `git diff`** antes de cada commit para no subir cosas de más.
7. Usar `git pull --rebase` si prefieres un historial lineal (avanzado).

---

## 12. Comandos en acción: ejemplo completo

```bash
# 1. Configurar una sola vez
git config --global user.name "Marcos Pérez"
git config --global user.email "marcos@correo.com"

# 2. Crear el proyecto local
mkdir mi-proyecto
cd mi-proyecto
git init

# 3. Crear la conexión con el remoto (ya creado en GitHub)
git remote add origin https://github.com/marcos/mi-proyecto.git

# 4. Primeros archivos y primer commit
git add README.md
git commit -m "Agregar README inicial"

# 5. Subir al remoto
git push -u origin main

# 6. Trabajar con una rama nueva
git checkout -b feature-precios
# ...modificas archivos...
git add .
git commit -m "Agregar cálculo de precios"
git push -u origin feature-precios

# 7. Fusionar en main
git checkout main
git pull
git merge feature-precios
git push

# 8. Bajar cambios que hizo otra persona
git pull
```

---

## 13. Glosario rápido

- **Repositorio**: donde se almacena el proyecto y su historial de cambios.
- **Commit**: una fotografía de los cambios en un momento dado.
- **Rama (branch)**: línea independiente de desarrollo.
- **Merge**: unir los cambios de una rama a otra.
- **Staging area**: zona intermedia entre los archivos modificados y el commit.
- **Remoto (remote)**: copia del repositorio alojada en otro lugar (ej. GitHub).
- **Clonar (clone)**: copiar un repositorio remoto a tu máquina.
- **Push**: subir commits locales al remoto.
- **Pull**: bajar commits del remoto a tu local.
- **Fetch**: bajar información del remoto sin modificar tu trabajo local.
- **Conflicto**: cuando Git no puede fusionar cambios automáticamente.
- **.gitignore**: archivo que indica qué archivos/carpetas ignorar.