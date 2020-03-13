# Instalación chocolatey sobre MS Windows

# Requisitos previos

Debe tener las variables `http_proxy` y `https_proxy` correctamente configuradas con `http://IP_PROXY:PUERTO_PROXY/` que usted tiene asignado en la sesión actual:

```
set | more
```

# Instalación


1. Abrir CMD como administrador.

2. Ejecutar el siguiente comando:
```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```
3. Comprobar salida exitosa:
```
Chocolatey (choco.exe) is now ready.
```

4. Verificar versión instalada:
```
C:\WINDOWS\system32>choco install
Chocolatey v0.10.15
Package name is required. Please pass at least one package name to install.
```

Link de referencia: https://chocolatey.org/install

