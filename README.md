# 🚀 Apuntes & Bitácora DevOps - KodeKloud

Bienvenido/a a mi repositorio de bitácoras, notas prácticas y resoluciones de desafíos de **DevOps, SysAdmin y MLOps** basados en los laboratorios prácticos de **KodeKloud** (como *100 Days of DevOps*, *100 Days of MLOps*, el path de **Linux Level 1** y desafíos de simulación en entornos de empresa como *xFusionCorp Industries*).

Actualmente mi foco principal está centrado en resolver los **desafíos de DevOps** y completar el path de **Linux Level 1** para consolidar y profundizar mis fundamentos de administración de sistemas y aplicarlos directamente en escenarios reales de infraestructura y DevOps.

---

## 🎯 Objetivos de Aprendizaje

El objetivo de este repositorio es documentar de forma clara, reproducible y estructurada mi camino de especialización en la cultura y tecnologías **DevOps / Cloud / SRE / MLOps**:

- **Aprender haciendo (Hands-on First):** Resolver problemas y escenarios del mundo real sobre infraestructura simulada multiserver y entornos de nube.
- **Profundizar Fundamentos Linux & DevOps:** Completar y documentar el path **Linux Level 1** y los desafíos diarios de DevOps para dominar la gestión de servidores, seguridad y resolución de problemas.
- **Crear una base de conocimiento y troubleshooting:** Registrar no solo los comandos necesarios para cumplir cada requerimiento, sino también el porqué de cada decisión, alternativas con y sin editores interactivos, validaciones y resolución de errores comunes.
- **Desarrollar consistencia y automatización:** Construir el hábito diario de resolver desafíos técnicos y sistematizar flujos con scripts y buenas prácticas.

---

## 🛠️ Tareas Diarias y Habilidades en Práctica

En el día a día de un ingeniero DevOps / SysAdmin / MLOps, las responsabilidades abarcan múltiples capas de la infraestructura. A través de estos ejercicios y del path **Linux Level 1**, se cubren tareas como:

| Área | Tareas Diarias Simuladas | Herramientas / Comandos Clave |
|---|---|---|
| **Linux Level 1 & SysAdmin** | Gestión de usuarios y grupos corporativos, control de permisos/propiedad (`chmod`/`chown`), caducidad de contraseñas, administración de archivos, directorios y procesos. | `useradd`, `usermod`, `groupadd`, `getent`, `chage`, `chmod`, `chown`, `sudo` |
| **Hardening y Seguridad** | Auditorías de seguridad, deshabilitación de accesos directos por `root`, configuración segura de demonios SSH en clústeres multiserver de *xFusionCorp*. | `/etc/ssh/sshd_config`, `PermitRootLogin`, `systemctl restart sshd`, `sshd -T` |
| **Troubleshooting & Diagnóstico DevOps** | Inspección de servicios web y aplicaciones, resolución de puertos erróneos, binding de interfaces (`0.0.0.0` vs loopback), validación de healthchecks HTTP y análisis de logs. | `curl -w "%{http_code}"`, `journalctl`, `nohup`, `netstat`/`ss`, logs en `/tmp` o `/var/log` |
| **Automatización y Manipulación de Texto** | Edición no interactiva de archivos de configuración sobre servidores remotos y pipelines de entrega continua. | `sed -i`, `grep -E`, `awk`, pipelines de Bash |
| **MLOps & Ambientes Reproducibles** | Configuración de entornos virtuales aislados para proyectos de Data Science/ML, congelamiento de dependencias y despliegue de servicios headless. | `python3 -m venv`, `pip freeze`, `requirements.txt`, `jupyter lab --config` |
| **Control de Versiones y GitOps** | Gestión de ramas, resolución de conflictos de merge, sincronización con repositorios remotos y versionado de infraestructura/documentación. | `git status`, `git branch`, `git checkout -b`, `git merge`, `git push` |

---

## 📁 Estructura del Repositorio

```text
Apuntes-KodeKloud/
│
├── 100 Days of DevOps.md      # Bitácora de tareas de SysAdmin, Linux, seguridad y DevOps
├── Linux Level 1.md           # Bitácora de tareas y laboratorios del path Linux Level 1
├── 100 Days of MLOps.md       # Bitácora de tareas de MLOps, virtual environments y despliegue de notebooks
├── Comandos y Flags.md        # Glosario y referencia rápida de comandos y sus flags
├── guia-git-github.md         # Guía completa de referencia y buenas prácticas con Git y GitHub
└── README.md                  # Presentación del proyecto y mapa de objetivos
```

---

## 📝 Formato de cada Desafío

Para mantener el máximo valor de aprendizaje, cada tarea documentada sigue un formato estándar:

1. **Requerimiento:** Contexto del escenario del negocio / ticket y condiciones de éxito esperadas.
2. **Resolución Paso a Paso:** Comandos detallados y comentados para replicar la solución.
3. **Alternativas de comando:** Enfoques directos con automatización (ej. uso de `sed` frente a edición manual).
4. **Notas / Troubleshooting & Verificación:** Pruebas de validación (healthchecks, inspección de servicios) y puntos clave a tener en cuenta para no romper producción.
5. **Glosario de comandos y flags:** Explicación detallada de cada flag utilizado en la sesión.

---

## 💻 Tecnologías y Herramientas

- **Sistemas Operativos:** Linux (Ubuntu, CentOS, RHEL, Debian)
- **Networking & Seguridad:** OpenSSH, SSH Hardening, Firewalls, Systemd
- **Lenguajes & Ecosistema:** Bash Scripting, Python 3, Pip, Virtual Environments
- **Herramientas & Plataformas:** KodeKloud Labs, JupyterLab, Git, GitHub

