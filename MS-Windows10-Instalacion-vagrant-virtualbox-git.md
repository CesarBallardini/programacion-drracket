# Instalación de  Vagrant y sus plugins / Virtualbox / Git sobre MS-Windows

Usaremos el programa chocolatey previamente instalado.

1.- En un CMD de Administrador, hacer:

```
choco install git virtualbox vagrant cyg-get

refreshenv

cyg-get openssh 
cyg-get rsync 
cyg-get ncurses
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



2.- En un CMD de la cuenta de usuario normal de trabajo, hacer:

```
vagrant plugin install vagrant-winnfsd
vagrant plugin install vagrant-vbguest
vagrant plugin install vagrant-proxyconf


```
3.- Configurar plugin proxyconf

Escribir el archivo: `%userprofile%/.vagrant.d/Vagrantfile`

```ruby
Vagrant.configure("2") do |config|
  $stderr.puts "proxyconf..."
#  if defined?(VagrantPlugins::ProxyConf)       # vagrant 1.2
  if Vagrant.has_plugin?("vagrant-proxyconf")   # otros vagrant
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
```


4.- Reiniciar el MS Windows.



## Verificar


1.- Crear un archivo `Vagrantfile`:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 2.1.0"

# Detect host OS for different folder share configuration
module OS
    def OS.windows?
        (/cygwin|mswin|mingw|bccwin|wince|emx/ =~ RUBY_PLATFORM) != nil
    end

    def OS.mac?
        (/darwin/ =~ RUBY_PLATFORM) != nil
    end

    def OS.unix?
        !OS.windows?
    end

    def OS.linux?
        OS.unix? and not OS.mac?
    end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "debian/stretch64"

  config.vm.provider "virtualbox"

# Check available Plugins
#  if OS.windows?
#      if !Vagrant.has_plugin?('vagrant-winnfsd')
#          puts "The vagrant-winnfsd plugin is required. Please install it with \"vagrant plugin install vagrant-winnfsd\""
#          exit
#      end
#  end

  if Vagrant.has_plugin?('vagrant-vbguest')
      config.vbguest.auto_update = true
  end

  config.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.customize ['modifyvm', :id, '--memory', 1024]
      vb.customize ["modifyvm", :id, "--cpus", 2]
      vb.customize ["modifyvm", :id, "--name", "vagrant-playground"]
  end

  # Configure the VM
  config.vm.hostname = 'vagrant-playground'
  config.vm.network :private_network, ip: "192.168.56.123"

  if OS.windows?
    #config.vm.synced_folder ".", "/vagrant", type: "nfs" # usar si tiene las guest additions instaladas el box
    config.vm.synced_folder ".", "/vagrant", type: "rsync"
  else
    config.vm.synced_folder ".", "/vagrant", :owner => "vagrant", :group => "vagrant"
  end

end

```

2.- Asegurar que las variables `http_proxy` y `https_proxy` están correctamente configuradas en la sesión actual:



```
set | more
```


3.- Ejecutar:

```
vagrant up

# esperar que arranque

vagrant ssh

```


## Referencias

* https://codingbee.net/vagrant/vagrant-installing-vagrant-on-windows
* https://github.com/neikei/install-vagrant-on-windows/blob/master/README.md

