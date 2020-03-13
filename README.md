# README - Programación en Dr Racket

Utilizaremos un notebook Jupyter con DrRacket para aprender Scheme y resolver algunos ejercicios.

Debes tener una instalación funcional de Git, VirtualBox y Vagrant en tu computadora.  Las instrucciones para conseguir esto están en el apéndice, más abajo,
según el sistema operativo de tu computadora.

---
# Conceptos de Jupyter Notebook

FIXME

---
# Cómo usar este repositorio

(Si los requisitos están satisfechos, puedes seguir aquí, sino busca cómo satisfacerlos en los apéndices.)

1. Clona el repositorio
```bash
git clone https://github.com/CesarBallardini/programacion-drracket
cd programacion-drracket/
```
2. Levanta la VM con Racket, Jupyter y el kernel iracket
```bash
vagrant up
```
3. Apunta tu navegador a http://127.0.0.1:8888/
4. Si la pantalla te solicita una password, oprime la tecla ENTER
5. Utiliza los notebooks en la ventana del navegador.
6. Cuando hayas terminado, no olvides grabar tus resultados si te interesa conservarlos
7. Apaga la VM con:
```bash
vagrant halt
```



---
# Apéndice A - Instalación de los requisitos de software para correr en una VM el Jupyter Notebook de Racket.

Para instalar de manera sencilla Git, Virtualbox y Vagrant, usaremos Chocolatey, un gestor de paquetes de software que es software libre.

Si no quieres usar Chocolatey, o si ya tienes instalados los requisitos, puede ignorar este apéndice.
Verifica que están corriendo estos mandatos en una ventana del programa cmd:

```bash
git --version
vboxmanage --version
vagrant --version
```

Si alguno falla, es que falta instalarlo.


## Instalación sobre MS-Windows 10

* [Instalación de Chocolatey](Instalacion-chocolatey.md)
* [Instalación de Vagrant, Virtualbox y Git](MS-Windows10-Instalacion-vagrant-virtualbox-git.md)

## Instalación sobre Ubuntu 18.04 (Bionic)

* [Instalación de Vagrant, Virtualbox y Git](Ubuntu-Bionic-Instalacion-vagrant-virtualbox-git.md)


