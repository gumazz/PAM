# Описание параметров ВМ
MACHINES = {
  # Имя DV "pam"
  :"pam" => {
              # VM box
              :box_name => "centos/stream8",
              #box_version
              #:box_version => "20210210.0",
              # Количество ядер CPU
              :cpus => 2,
              # Указываем количество ОЗУ (В Мегабайтах)
              :memory => 1024,
              # Указываем IP-адрес для ВМ
              :ip => "192.168.57.10",
            }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    # Отключаем сетевую папку
    #config.vm.synced_folder ".", "/vagrant", disabled: true
    # Добавляем сетевой интерфейс
    config.vm.network "private_network", ip: boxconfig[:ip]
    # Применяем параметры, указанные выше
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s

      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
      end
      box.vm.provision "shell", inline: <<-SHELL
	  mkdir -p ~root/.ssh
          cp ~vagrant/.ssh/auth* ~root/.ssh
          #Разрешаем подключение пользователей по SSH с использованием пароля
          sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
          #Перезапуск службы SSHD
          systemctl restart sshd.service
	  setenforce 0
	  sudo useradd otusadm && sudo useradd otus
	  echo "Otus2022!" | sudo passwd --stdin otusadm && echo "Otus2022!" | sudo passwd --stdin otus
	  sudo groupadd -f admin
	  usermod otusadm -a -G admin && usermod root -a -G admin && usermod vagrant -a -G admin
	  cp -a /vagrant/login.sh /usr/local/bin/
	  chmod +x /usr/local/bin/login.sh
	  echo "auth required pam_exec.so /usr/local/bin/login.sh" >> /etc/pam.d/sshd
  	  SHELL
    end
  end
end

