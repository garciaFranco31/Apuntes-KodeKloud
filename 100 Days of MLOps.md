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