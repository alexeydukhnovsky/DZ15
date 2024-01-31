# Описание параметров ВМ
MACHINES = {
  # Имя DV "pam"
  :"pam" => {
              # VM box
	      :box_name => "centos/7",
              :box_version => "2004.01",              
              #:box_name => "centos/stream8",
              #box_version
              #:box_version => "20210210.0",
              # Количество ядер CPU
              :cpus => 2,
              # Указываем количество ОЗУ (В Мегабайтах)
              :memory => 1024,
              # Указываем IP-адрес для ВМ
              :ip => "192.168.56.10",
            }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    # Отключаем сетевую папку
    config.vm.synced_folder ".", "/vagrant", disabled: false
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
          #Разрешаем подключение пользователей по SSH с использованием пароля
          sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
          #Перезапуск службы SSHD
          systemctl restart sshd.service
          sudo -i
	  useradd otusadm && sudo useradd otus
	  echo "Otus2024" | sudo passwd --stdin otusadm && echo "Otus2024" | sudo passwd --stdin otus
	  groupadd -f admin
          usermod otusadm -a -G admin && usermod root -a -G admin && usermod vagrant -a -G admin
	  cp /vagrant/login.sh /usr/local/bin/
	  chmod +x /usr/local/bin/login.sh
	  rm -rf /etc/pam.d/sshd
	  cp /vagrant/sshd /etc/pam.d/
          systemctl restart sshd.service
  	  SHELL
    end
  end
end
