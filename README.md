# 🚀 Apuntes & Bitácora DevOps - KodeKloud

Bienvenido/a a mi repositorio de bitácoras, notas prácticas y resoluciones de desafíos de **DevOps, SysAdmin y MLOps** basados en los laboratorios prácticos de **KodeKloud** (como *100 Days of DevOps*, *100 Days of MLOps* y desafíos de simulación en entornos de empresa como *xFusionCorp Industries*).

---

## 🎯 Objetivos de Aprendizaje

El objetivo de este repositorio es documentar de forma clara, reproducible y estructurada mi camino de especialización en la cultura y tecnologías **DevOps / Cloud / SRE / MLOps**:

- **Aprender haciendo (Hands-on First):** Resolver problemas y escenarios del mundo real sobre infraestructura simulada multiserver y entornos de nube.
- **Crear una base de conocimiento y troubleshooting:** Registrar no solo los comandos necesarios para cumplir cada requerimiento, sino también el porqué de cada decisión, alternativas con y sin editores interactivos, validaciones y resolución de errores comunes.
- **Desarrollar consistencia y automatización:** Construir el hábito diario de resolver desafíos técnicos y sistematizar flujos con scripts y buenas prácticas.

---

## 🛠️ Tareas Diarias y Habilidades en Práctica

En el día a día de un ingeniero DevOps / SysAdmin / MLOps, las responsabilidades abarcan múltiples capas de la infraestructura. A través de estos ejercicios se cubren tareas como:

| Área | Tareas Diarias Simuladas | Herramientas / Comandos Clave |
|---|---|---|
| **Administración de Sistemas Linux** | Creación y administración de usuarios temporales y corporativos con fechas de caducidad, gestión de grupos, permisos y cuotas de disco. | `useradd`, `usermod`, `chage`, `sudo`, `/etc/passwd`, `/etc/shadow` |
| **Hardening y Seguridad** | Auditorías de seguridad, deshabilitación de accesos directos por `root`, configuración segura de demonios SSH en clústeres multiserver. | `/etc/ssh/sshd_config`, `PermitRootLogin`, `systemctl restart sshd`, `sshd -T` |
| **Troubleshooting y Diagnóstico** | Inspección de configuraciones incorrectas en servicios web/aplicaciones, resolución de puertos erróneos, interfaces no alcanzables (`0.0.0.0` vs loopback), validación de healthchecks HTTP. | `curl -w "%{http_code}"`, `journalctl`, `nohup`, `netstat`/`ss`, logs en `/tmp` o `/var/log` |
| **Automatización y Manipulación de Texto** | Edición no interactiva de archivos de configuración sobre servidores remotos y pipelines. | `sed -i`, `grep -E`, `awk`, pipelines de Bash |
| **MLOps & Ambientes Reproducibles** | Configuración de entornos virtuales aislados para equipos de Data Science/ML, freeze de dependencias para reproducibilidad y despliegue de servicios headless. | `python3 -m venv`, `pip freeze`, `requirements.txt`, `jupyter lab --config` |
| **Control de Versiones y GitOps** | Gestión de ramas, resolución de conflictos de merge, sincronización con repositorios remotos y versionado de infraestructura como código/documentación. | `git status`, `git branch`, `git checkout -b`, `git merge`, `git push` |

---

## 📁 Estructura del Repositorio

```text
Apuntes-KodeKloud/
│
├── 100 Days of DevOps.md      # Bitácora de tareas de SysAdmin, Linux, seguridad y DevOps
├── 100 Days of MLOps.md       # Bitácora de tareas de MLOps, virtual environments y despliegue de notebooks
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
