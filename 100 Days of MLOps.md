# 100 Days DevOps Challenge - Bitácora 

## Tarea 1: Set up a Python Virtual Environment (Machine Learning)

### Requerimiento

The xFusionCorp Industries data science team requires a standardized Python environment for their new machine learning project. Set up a virtual environment on the controlplane host that includes all necessary ML libraries.

The work is done on the controlplane host under /root/code/.

The end state must satisfy the following:

1. A Python virtual environment named `ml-env` exists under `/root/code/`;
2. The environment has `numpy`, `pandas`, `scikit-learn`, and `matplotlib` installed;
3. A `requirements.txt` capturing the installed packages is saved at `/root/code/requirements.txt`.

### Resolución

```bash
# 1. Crear la carpeta y el virtual environment
mkdir -p /root/code
cd /root/code
python3 -m venv ml-env

# 2. Activar el environment e instalar las librerías
source ml-env/bin/activate
pip install numpy pandas scikit-learn matplotlib

# 3. Generar requirements.txt (dentro de ml-env activado)
pip freeze > /root/code/requirements.txt

# 4. Verificar
pip list                # confirma que están instaladas
cat /root/code/requirements.txt
```

### Notas / Troubleshooting

- Si salta un warning de que `pip` está viejo, actualizarlo con `python3 -m pip install --upgrade pip`.
- En caso de que no haya `python3 -m venv` disponible, tal vez sea necesario `apt install python3-venv` (según la imagen del lab).
- Es normal que `pip install` tarde un poco porque instala dependencias (scikit-learn trae muchas).
- Trabajo a realizar en el host `controlplane`, bajo `/root/code/`.

---

## Tarea 2: Arreglar y levantar el servidor JupyterLab

### Requerimiento

A teammate has configured a JupyterLab server for the xFusionCorp Industries data science team; however, the server is not functioning as expected. Inspect the configuration, diagnose any issues, and start the server.

JupyterLab is already installed in the virtual environment at `/root/code/ml-env/`. The team's configuration file is at `/root/code/jupyter_lab_config.py` and is visible in the file explorer. Start the server with the config (e.g. `/root/code/ml-env/bin/jupyter lab --config /root/code/jupyter_lab_config.py`) and observe how it comes up so you can see what is misconfigured.

The end state must satisfy the following:

- The running server listens on port `8888`;
- It binds on `0.0.0.0`;
- The notebook root directory is `/root/notebooks/`, and that directory exists on disk;
- With the configuration corrected and JupyterLab running, the Jupyter UI button at the top of the lab opens the notebook interface.

### Resolución

```bash
# 1. Activar el virtual environment (el binario jupyter apunta al del sandbox)
source /root/code/ml-env/bin/activate

# 2. Inspeccionar la configuración actual
cat /root/code/jupyter_lab_config.py
# Los 3 valores incorrectos del config original son:
#   c.ServerApp.root_dir = '/root/wrong-path'   -> el directorio no existe
#   c.ServerApp.port = 8000                     -> el botón de la plataforma espera el 8888
#   c.ServerApp.ip = '1.1.1.1'                  -> dirección no enrutable, el proxy no llega

# 3. Corregir los 3 settings con sed (edit in place)
sed -i "s|c.ServerApp.root_dir = '/root/wrong-path'|c.ServerApp.root_dir = '/root/notebooks'|" /root/code/jupyter_lab_config.py
sed -i "s|c.ServerApp.port = 8000|c.ServerApp.port = 8888|" /root/code/jupyter_lab_config.py
sed -i "s|c.ServerApp.ip = '1.1.1.1'|c.ServerApp.ip = '0.0.0.0'|" /root/code/jupyter_lab_config.py

# 4. Crear el directorio raíz de notebooks (JupyterLab no arranca si no existe)
mkdir -p /root/notebooks

# 5. Iniciar JupyterLab en segundo plano
nohup jupyter lab --config=/root/code/jupyter_lab_config.py --allow-root --no-browser > /tmp/jupyter.log 2>&1 &

# 6. Verificar
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8888/lab
grep -E "root_dir|ServerApp.ip|ServerApp.port" /root/code/jupyter_lab_config.py
ls /root/notebooks/
```

### Notas / Troubleshooting

- El servidor debe **bindear en `0.0.0.0`** (todas las interfaces), no en loopback o una dirección no enrutable, porque el proxy del lab lo alcanza a través de todas las interfaces.
- `--config=/ruta` apunta a Jupyter al archivo de configuración; sin él Jupyter usa los defaults.
- `--allow-root` es obligatorio porque el lab corre como usuario root.
- `--no-browser` evita que Jupyter intente abrir un navegador en un host headless.
- Se corre con `nohup ... &` para que siga vivo en segundo plano y redirige el log a `/tmp/jupyter.log`.
- El directorio `/root/notebooks` **debe existir antes** de arrancar, si no el servidor se niega a lanzarse.
- Verificación final: el `curl` debe devolver `200` (o `302`), el `grep` debe mostrar los 3 valores corregidos y `ls` debe listar el contenido de `/root/notebooks`.

---

## Comandos utilizados en este día

| Comando | Flag | Descripción |
|---|---|---|
| `source /root/code/ml-env/bin/activate` | — | Activa el virtual environment `ml-env` para que el comando `jupyter` resuelva el binario del sandbox |
| `cat /root/code/jupyter_lab_config.py` | — | Imprime en terminal el contenido del archivo de configuración para inspeccionarlo |
| `sed -i "s\|old\|new\|" archivo` | `-i` | Edita el archivo **in place** (guarda los cambios en el propio archivo, no muestra por pantalla) |
| | `s\|old\|new\|` | Sustituye la primera ocurrencia de `old` por `new` en cada línea (usamos `\|` como delimitador para no escapar las `/` de las rutas) |
| `mkdir -p /root/notebooks` | `-p` | Crea el directorio junto con los padres que falten; no da error si ya existe |
| `nohup jupyter lab --config=/root/code/jupyter_lab_config.py --allow-root --no-browser > /tmp/jupyter.log 2>&1 &` | `nohup` | Ignora la señal HUP: el proceso sigue ejecutándose aunque se cierre la sesión/terminal |
| | `--config=/ruta` | Indica a JupyterLab qué archivo de configuración usar en lugar de los defaults |
| | `--allow-root` | Permite arrancar el servidor siendo usuario **root** (obligatorio en este lab) |
| | `--no-browser` | No intenta abrir un navegador (host headless), solo deja el servidor escuchando |
| | `> archivo 2>&1` | Redirige la salida estándar (stdout, `1`) y de errores (stderr, `2`) al mismo archivo de log |
| | `&` | Lanza el comando en **segundo plano** y devuelve el prompt |
| `curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8888/lab` | `-s` | Modo silencioso: suprime la barra de progreso y mensajes de error de la salida |
| | `-o /dev/null` | Descarta el cuerpo de la respuesta, no lo imprime |
| | `-w "%{http_code}\n"` | Imprime solo el código de estado HTTP de la respuesta (ej. `200`) |
| `grep -E "root_dir\|ServerApp.ip\|ServerApp.port" archivo` | `-E` | Usa expresiones regulares extendidas para buscar los patrones en el archivo |
| `ls /root/notebooks/` | — | Lista el contenido del directorio raíz de notebooks para confirmar que existe y qué contiene |