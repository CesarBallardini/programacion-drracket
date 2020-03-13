# Instalación de  Vagrant y sus plugins / Virtualbox / Git sobre Ubuntu 18.04 (Bionic)


Si ya tienes instalados Git, Virtualbox y Vagrant, puedes ignorar este apéndice. Verifica que están corriendo estos mandatos en una terminal de shell:

```bash
git --version
vboxmanage --version
vagrant --version
```

Si alguno falla, es que falta instalarlo.

Los mandatos que se usan a continuación se ejecutan mediante `sudo`, así que debes tenerlo configurado adecuadamente para correr mandatos como `root` 
desde la cuenta de usuario que usas en tu computadora.

## Instalación de programas auxiliares

Estos programas se usan en las instrucciones más abajo.

```bash
sudo apt-get update
sudo apt-get install -y -q apt-transport-https ca-certificates

[ -x /usr/bin/jq ]      || sudo apt-get install jq -y -q
[ -x /usr/bin/curl ]    || sudo apt-get install curl -y -q
[ -x /usr/bin/wget ]    || sudo apt-get install wget -y -q
[ -x /usr/bin/sshpass ] || sudo apt-get install sshpass -y -q

```


## Instalación de git

```bash
sudo apt-get install git -y -q
```

Configuración inicial de Git:

```bash

##
# sustituye por tu nombre completo y tu dirección de email:
#
git config --global user.name "Juan Perez"
git config --global user.email juanperez@example.com

# use colores cuando pueda
git config --global color.ui auto

# default ya esta seleccionado
git config --global push.default simple

# guarde credenciales durante un dia en cache para no repreguntarlas
git config --global credential.helper cache --timeout 36000

```

## Instalación de Virtualbox


Agregamos la fuente de paquetes .deb de Virtualbox, la clave del repositorio y los instalamos.  Luego buscamos cuál es el Extension Pack que le corresponde
a esa versión instalada, lo descargamos y lo instalamos también.

```bash

echo "deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian $(lsb_release -c -s) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list

wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -

sudo apt-get update
sudo apt-get install linux-headers-$(uname -r) dkms -y
sudo apt-get remove --purge virtualbox virtualbox-5.1 virtualbox-5.2 -y
sudo apt-get install virtualbox-6.0 -y

vb_long_version=$( VBoxManage --version )
vb_short_version=$( echo $vb_long_version | sed -e "s@\([0-9]*\.[0-9]*\.[0-9]*\)r.*@\\1@" )

wget \
  --output-document=/tmp/Oracle_VM_VirtualBox_Extension_Pack-${vb_long_version}.vbox-extpack \
  https://download.virtualbox.org/virtualbox/${vb_short_version}/Oracle_VM_VirtualBox_Extension_Pack-$(echo ${vb_long_version} | tr "r" "-"  ).vbox-extpack

echo 'y' | sudo VBoxManage extpack install --replace /tmp/Oracle_VM_VirtualBox_Extension_Pack-${vb_long_version}.vbox-extpack


```


## Instalación de Vagrant

Descargamos la lista de programas que fabrica Hashi Corp, el productor de Vagrant. Ubicamos cuál es la última versión estable de Vagrant, descargamos
el paquete .deb y lo instalamos.

```bash
export HASHICORP_RELEASES_FILE=$( mktemp --suffix=HASHICORP_RELEASES )
curl --silent https://releases.hashicorp.com/index.json > ${HASHICORP_RELEASES_FILE}

vagrant_url_version=$( cat "${HASHICORP_RELEASES_FILE}" |  jq '.vagrant.versions| .[] | .version' | sort -rV | sed -n  's/\("[0-9]*\.[0-9]*\.[0-9]*"\)/\1/p' | head -n1  | tr -d '"')
vagrant_deb=vagrant_${vagrant_url_version}_x86_64.deb
vagrant_url=https://releases.hashicorp.com/vagrant/${vagrant_url_version}/${vagrant_deb}
wget -c -O /tmp/${vagrant_deb} ${vagrant_url}
sudo dpkg -i /tmp/${vagrant_deb}
sudo apt-get install -f

```


## Instalación de los plugins para Vagrant

Los plugins de Vagrant nos hacen la vida más fácil.  Es largo de explicar aquí: busca la documentación de cada uno si deseas aprender más.

```bash
# plugins esenciales:
vagrant plugin install vagrant-disksize
vagrant plugin install vagrant-vbguest

# plugins adicionales
vagrant plugin install hostmanager
vagrant plugin install vagrant-cachier
vagrant plugin install vagrant-hostmanager
vagrant plugin install vagrant-share
vagrant plugin install vagrant-mutate
vagrant plugin install vagrant-rekey-ssh

```


Configuramos el plugin `vagrant-proxyconf` si nuestro acceso a internet es a través de un proxy corporativo que ya está configurado correctamente en nuestra computadora:

```bash
# lo instalamos
vagrant plugin install vagrant-proxyconf

mkdir ~/.vagrant.d/

cat <<-'EOF' > ~/.vagrant.d/Vagrantfile
Vagrant.configure("2") do |config|
  $stderr.puts "proxyconf..."
  if Vagrant.has_plugin?("vagrant-proxyconf")
    $stderr.puts "se encontro el plugin proxyconf !"
    if ENV["http_proxy"]
      $stderr.puts "http_proxy: " + ENV["http_proxy"]
      config.proxy.http     = ENV["http_proxy"]
    end
    if ENV["https_proxy"]
      $stderr.puts "https_proxy: " + ENV["https_proxy"]
      config.proxy.https    = ENV["https_proxy"]
    end
    if ENV["no_proxy"]
      $stderr.puts "no_proxy: " + ENV["no_proxy"]
      config.proxy.no_proxy = ENV["no_proxy"]
    end
  else
    $stderr.puts "no se encontro el plugin proxyconf !"
  end
end
EOF

```


