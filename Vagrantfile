VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "bento/centos-7.4"
  config.vm.hostname = "co74xfcefreeplane"
  config.vm.provider :virtualbox do |vbox|
     vbox.name = "co74xfcefreeplane"
     vbox.gui = true
     vbox.cpus = 4
     vbox.memory = 4096
     vbox.customize ["modifyvm", :id, "--nicpromisc2","allow-all"]
  end
#  config.vm.network "public_network", ip: "192.168.0.16", :netmask => "255.255.255.0"
  config.vm.network "private_network", ip: "192.168.97.16" , :netmask => "255.255.255.0"
  config.vm.provision "shell", inline: <<-SHELL
localectl set-locale LANG=zh_TW.UTF-8
yum install -y epel-release
yum check-update
yum -y update

yum -y groupinstall yum groupinstall "X Window System"  Xfce "Input Methods"
yum -y install vlgothic-*  ipa-gothic-fonts ipa-mincho-fonts ipa-pgothic-fonts ipa-pmincho-fonts xrdp

cat << EOF >> /home/vagrant/.Xclients
export GTK_IM_MODULE=ibus
export XMODIFIERS=@im=ibus
export QT_IM_MODULE=ibus
ibus-daemon -drx
exec /usr/bin/xfce4-session
EOF
chmod +x /home/vagrant/.Xclients
chown vagrant:vagrant /home/vagrant/.Xclients

# 不要なダイアログを表示されないようにする
sudo sh -c "echo 'X-GNOME-Autostart-enabled=false' >> /etc/xdg/autostart/gnome-welcome-tour.desktop"
sudo sh -c "echo 'X-GNOME-Autostart-enabled=false' >> /etc/xdg/autostart/gnome-software-service.desktop"
sudo sh -c "echo 'X-GNOME-Autostart-enabled=false' >> /etc/xdg/autostart/gnome-settings-daemon.desktop"
sudo systemctl disable initial-setup-graphical.service
sudo systemctl disable initial-setup-text.service

systemctl set-default graphical.target

systemctl enable xrdp.service
systemctl start xrdp.service

# dockerをインストール
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --disable docker-ce-edge
yum -y install docker-ce
systemctl enable docker
systemctl start docker
usermod -a -G docker vagrant

# install docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# install of git
yum install -y git
git clone  https://github.com/robert0714/maas-dev-docekr-V2.git /home/vagrant/Desktop/
cd   /home/vagrant/Desktop/
docker-compose -f  /home/vagrant/Desktop/docker-compose.yml  up  -d

# javaをインストール
yum -y install java-1.8.0-openjdk


wget http://ftp.jaist.ac.jp/pub/eclipse/technology/epp/downloads/release/oxygen/1/eclipse-java-oxygen-1-linux-gtk-x86_64.tar.gz
tar xvfz eclipse-java-oxygen-1-linux-gtk-x86_64.tar.gz
mv eclipse /opt

# eclipse oxygenのショートカットを作成
cat << EOF > /usr/share/applications/eclipse.desktop
#!/usr/bin/env xdg-open
[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Exec=/opt/eclipse/eclipse
Name=eclipse
Categories=Utility;Development;
EOF



telinit 5
SHELL
end
