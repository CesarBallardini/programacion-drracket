# Instalación manual de Jupyter Notebook sobre Ubuntu 18.04

# 1. Racket

```bash
sudo add-apt-repository ppa:plt/racket -y
sudo apt-get update
sudo apt-get install racket racket-doc -y

```

# 2. Jupyter

```bash
sudo apt-get install -y python3-pip
sudo -H python3 -m pip install --upgrade pip
#sudo -H python3 -m pip install --upgrade jupyterlab
sudo -H python3 -m pip install --upgrade notebook
```

# 3. ZeroMQ

```bash
sudo apt-get install libzmq5 -y
```

# 4. Instalar paquete `iracket`

```bash
# dependencias
raco pkg install zeromq-r-lib  uuid sha


# instalar:
raco pkg install iracket

# Registrar el kernel en Jupyter:
raco iracket install
```

# 5. Ejecutar un `notebook`

```bash
# cd /directorio/donde/guardas/los/notebooks/
jupyter notebook --no-browser --port 9999
```
