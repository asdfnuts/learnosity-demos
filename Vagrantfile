# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.host_name = "learnosity-demo.dev"

  config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     apt-get install -y vim curl git-core
     apt-get install -y php5 apache2 libapache2-mod-php5 php5-curl php5-gd php5-mcrypt

     # Install composer
     php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
     php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
     php composer-setup.php --install-dir=/usr/local/bin --filename=composer
     php -r "unlink('composer-setup.php');"

     # Install dependencies
     su - vagrant -c 'cd /vagrant; composer --no-interaction --quiet install'

     # Make us the default site
     sed -i 's^DocumentRoot.*^DocumentRoot /vagrant/www^' /etc/apache2/sites-available/000-default.conf
     cat << EOF > /etc/apache2/conf-available/vagrant-www.conf
<Directory /vagrant/www>
	Options Indexes FollowSymLinks
	AllowOverride None
	Require all granted
</Directory>
EOF
     ln -s ../conf-available/vagrant-www.conf /etc/apache2/conf-enabled/
     service apache2 restart
  SHELL
end
