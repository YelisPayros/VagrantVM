Vagrant.configure("2") do |config|
  # Configuración de la primera máquina virtual (Servidor Apache 1)
  config.vm.define "apache1" do |apache1|
    apache1.vm.box = "hashicorp/bionic64"
    apache1.vm.hostname = "apache1"
    apache1.vm.network "private_network", ip: "192.168.33.10"
    apache1.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    apache1.vm.synced_folder "./pagina_web", "/var/www/html", create: true
    apache1.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      systemctl enable apache2
      systemctl start apache2
      rm -f /var/www/html/*  # Elimina cualquier archivo existente
      echo "Hola desde Apache1" | sudo tee /var/www/html/index1.html
    SHELL
  end

  # Configuración de la segunda máquina virtual (Servidor Apache 2)
  config.vm.define "apache2" do |apache2|
    apache2.vm.box = "hashicorp/bionic64"
    apache2.vm.hostname = "apache2"
    apache2.vm.network "private_network", ip: "192.168.33.11"
    apache2.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    apache2.vm.synced_folder "./pagina_web", "/var/www/html", create: true
    apache2.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache2
      systemctl enable apache2
      systemctl start apache2
      rm -f /var/www/html/*  # Elimina cualquier archivo existente
      echo "Hola desde Apache2" | sudo tee /var/www/html/index2.html
    SHELL
  end

  # Configuración del tercer servidor (Nginx con Load Balancing)
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "hashicorp/bionic64"
    nginx.vm.hostname = "nginx"
    nginx.vm.network "private_network", ip: "192.168.33.12"
    nginx.vm.provider "virtualbox" do |vb|
      vb.memory = "512"
      vb.cpus = 1
    end
    nginx.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      systemctl enable nginx
      systemctl start nginx

      # Configurar Nginx como balanceador de carga
      cat <<EOL | sudo tee /etc/nginx/sites-available/load_balancer
      upstream backend {
          server 192.168.33.10;
          server 192.168.33.11;
      }

      server {
          listen 80;
          location / {
              proxy_pass http://backend;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_http_version 1.0;  # Opcional: desactiva keep-alive para pruebas
          }
      }
      EOL

      # Eliminar el sitio predeterminado y habilitar el balanceador
      sudo rm -f /etc/nginx/sites-enabled/default  # Elimina el enlace predeterminado
      sudo ln -sf /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/load_balancer

      # Verificar la configuración y reiniciar Nginx
      sudo nginx -t  # Verifica la sintaxis
      if [ $? -eq 0 ]; then
          sudo systemctl restart nginx
      else
          echo "Error en la configuración de Nginx"
          exit 1
      fi
    SHELL
  end
end