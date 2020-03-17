# -*- mode: ruby -*-
# vi: set ft=ruby :

# Para aprovechar este Vagrantfile necesita Vagrant y Virtualbox instalados:
#
#   * Virtualbox
#
#   * Vagrant
#
#   * Plugins de Vagrant:
#       + vagrant-proxyconf y su configuracion si requiere de un Proxy para salir a Internet
#       + vagrant-cachier
#       + vagrant-disksize
#       + vagrant-hostmanager
#       + vagrant-share
#       + vagrant-vbguest

VAGRANTFILE_API_VERSION = "2"

HOSTNAME = "drracket"


$post_up_message = <<POST_UP_MESSAGE
------------------------------------------------------
DrRacket Jupyter Notebook

URLS:
 - host only - http://192.168.33.11:8888/
 - Forwarded - http://localhost:9999/

El directorio ./notebooks/ es donde se guardan y leen los notebooks

------------------------------------------------------
POST_UP_MESSAGE


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.post_up_message = $post_up_message

  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
    config.hostmanager.ignore_private_ip = false
    config.hostmanager.include_offline = true

    # uso cachier con NFS solamente si el hostmanager gestiona los nombres en /etc/hosts del host
    if Vagrant.has_plugin?("vagrant-cachier")

      config.cache.auto_detect = false
      # W: Download is performed unsandboxed as root as file '/var/cache/apt/archives/partial/xyz' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)

      config.cache.synced_folder_opts = {
        owner: "_apt"
      }
      # Configure cached packages to be shared between instances of the same base box.
      # More info on http://fgrehm.viewdocs.io/vagrant-cachier/usage
      config.cache.scope = :box

      # OPTIONAL: If you are using VirtualBox, you might want to use that to enable
      # NFS for shared folders. This is also very useful for vagrant-libvirt if you
      # want bi-directional sync

      # NOTA: con encrypted HOME, no funciona el NFS, si no es tu caso, descomenta los parametros siguientes:
      #config.cache.synced_folder_opts = {
      #  type: :nfs,
      #  # The nolock option can be useful for an NFSv3 client that wants to avoid the
      #  # NLM sideband protocol. Without this option, apt-get might hang if it tries
      #  # to lock files needed for /var/cache/* operations. All of this can be avoided
      #  # by using NFSv4 everywhere. Please note that the tcp option is not the default.
      #  mount_options: ['rw', 'vers=3', 'tcp', 'nolock', 'fsc' , 'actimeo=2']
      #}

      # For more information please check http://docs.vagrantup.com/v2/synced-folders/basic_usage.html
   end

  end

 config.vm.define HOSTNAME do |srv|

    srv.vm.box = "ubuntu/bionic64"
    srv.disksize.size = '10GB'
    #srv.disksize.size = '20GB'


    srv.vm.network "private_network", ip: "192.168.33.11"
    srv.vm.network "forwarded_port", guest: 8888, host: 9999

    srv.vm.boot_timeout = 3600
    srv.vm.box_check_update = true
    srv.ssh.forward_agent = true
    srv.ssh.forward_x11 = true
    srv.vm.hostname = HOSTNAME

    if Vagrant.has_plugin?("vagrant-hostmanager")
      srv.hostmanager.aliases = %W(#{HOSTNAME+".dominio.local.tld'"} #{HOSTNAME} )
    end

    if Vagrant.has_plugin?("vagrant-vbguest") then
        srv.vbguest.auto_update = true
        srv.vbguest.no_install = false
    end

    srv.vm.synced_folder ".", "/vagrant", disabled: false, SharedFoldersEnableSymlinksCreate: false


    srv.vm.provider :virtualbox do |vb|
      vb.gui = false
      vb.cpus = 2
      vb.memory = "1024"

      # https://www.virtualbox.org/manual/ch08.html#vboxmanage-modifyvm mas parametros para personalizar en VB
    end
  end

    ##
    # Aprovisionamiento
    #
    config.vm.provision "fix-no-tty", type: "shell" do |s|
        s.privileged = false
        s.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
    end
    config.vm.provision "actualiza", type: "shell" do |s|  # http://foo-o-rama.com/vagrant--stdin-is-not-a-tty--fix.html
        s.privileged = false
        s.inline = <<-SHELL
          export DEBIAN_FRONTEND=noninteractive
          export APT_LISTCHANGES_FRONTEND=none
          export APT_OPTIONS=' -y --allow-downgrades --allow-remove-essential --allow-change-held-packages -o Dpkg::Options::=--force-confdef -o Dpkg::Options::=--force-confold '

          sudo -E apt-get --purge remove apt-listchanges -y > /dev/null 2>&1
          sudo -E apt-get update -y -qq > /dev/null 2>&1
          sudo dpkg-reconfigure --frontend=noninteractive libc6 > /dev/null 2>&1
          sudo -E apt-get -q --option "Dpkg::Options::=--force-confold" --assume-yes install libssl1.1  > /dev/null 2>&1 # https://bugs.launchpad.net/ubuntu/+source/openssl/+bug/1832919
          sudo -E apt-get install linux-image-generic ${APT_OPTIONS} || true
          sudo -E apt-get install linux-image-amd64 ${APT_OPTIONS}  || true
          sudo -E apt-get upgrade ${APT_OPTIONS} > /dev/null 2>&1
          sudo -E apt-get dist-upgrade ${APT_OPTIONS} > /dev/null 2>&1
          sudo -E apt-get autoremove -y > /dev/null 2>&1
          sudo -E apt-get autoclean -y > /dev/null 2>&1
          sudo -E apt-get clean > /dev/null 2>&1
        SHELL
    end
    config.vm.provision "ssh_pub_key", type: :shell do |s|
      begin
          ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
          s.inline = <<-SHELL
            mkdir -p /root/.ssh/
            touch /root/.ssh/authorized_keys
            echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
            echo #{ssh_pub_key} >> /root/.ssh/authorized_keys
          SHELL
      rescue
          puts "No hay claves publicas en el HOME de su pc"
          s.inline = "echo OK sin claves publicas"
      end
    end

    config.vm.provision "instala_racket_jupyter", type: :shell do |s|
        s.privileged = false
        s.inline = <<-SHELL

          sudo apt-get update

          # pandoc y xetext para generar PDF
          sudo apt-get install pandoc texlive-xetex -y

          sudo apt-get install -y python3-pip
          sudo -H python3 -m pip install --upgrade pip
          sudo -H python3 -m pip install --upgrade notebook

          # swi-prolog
          sudo apt-get install swi-prolog-nox
          python3 -m pip install --upgrade --user jswipl
          mkdir  ~/.local/share/jupyter/kernels/jswipl/
          wget https://raw.githubusercontent.com/targodan/jupyter-swi-prolog/master/kernel.json --output-document=$HOME/.local/share/jupyter/kernels/jswipl/kernel.json

          ## racket
          sudo add-apt-repository ppa:plt/racket -y ; sudo apt-get update
          sudo apt-get install racket racket-doc -y
          sudo apt-get install libzmq5 -y # ZeroMQ
          raco pkg install zeromq-r-lib uuid sha # Instalar paquete `iracket` / dependencias
          raco pkg install iracket
          raco iracket install # Registrar el kernel en Jupyter:

          # Cuando pide la contraseña solo hace falta oprimir la tecla ENTER
          jupyter notebook --generate-config
          echo "c.NotebookApp.password='$(python3 -c "from notebook.auth import passwd; print(passwd(''))")'" >>  ~/.jupyter/jupyter_notebook_config.py

        SHELL
    end

    config.trigger.after :reload, :up do |trigger| 
      trigger.name = "ejecuta_notebook"
      trigger.run_remote = { inline: <<-SHELL
          su --command "rm -f /vagrant/jupyter.out ; cd /vagrant/notebooks/ ; /usr/bin/nohup jupyter notebook --no-browser --ip 0.0.0.0 --port 8888 > /vagrant/jupyter.out 2>&1 &" --login vagrant
      SHELL
      }
    end

    config.trigger.before [:destroy, :reload, :halt] do |trigger|
      trigger.name = "apaga notebook"
      trigger.run_remote = { inline: <<-SHELL
         if [ -f /vagrant/jupyter.out ]
         then
           pid=$(lsof -Fp /vagrant/jupyter.out  | sed -n "s/^p\([0-9]*\)/\1/p")
           [ -z "${pid}" ] || kill "${pid}"
           sleep 2
           [ -z "${pid}" ] || kill -9 "${pid}"
         fi
      SHELL
      }
    end


#    proxy_host_port = ENV['all_proxy'] || ENV['http_proxy']  || ""
#    proxy_host_port = if proxy_host_port.empty? then "" else proxy_host_port.scan(/\/\/([0-9\.]*):/)[0][0]+':'+proxy_host_port.scan(/:([0-9]*)$/)[0][0] end

end
