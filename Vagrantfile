# -*- mode: ruby -*-
# vi: set ft=ruby :

RUBY_V = '2.6.2'
RAILS_V = '5.2.3'

$apt_script = <<SCRIPT
sudo apt-get update
sudo apt-get install -y git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev libmysqlclient-dev
SCRIPT

$mysql_script = <<SCRIPT
echo 'installing mysql with root/root user'
debconf-set-selections <<< 'mysql-server mysql-server/root_password password root'
debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password root'
sudo apt-get  install -y mysql-server
SCRIPT

$node_script = <<SCRIPT
echo 'installing nvm/nodejs'
curl -sL https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh -o install_nvm.sh
bash install_nvm.sh
export PATH="$HOME/.nvm:$PATH"
source ~/.nvm/nvm.sh
nvm install 10.10.0
nvm use 10.10.0
SCRIPT

$yarn_script = <<SCRIPT
#echo 'installing yarn'
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt remove cmdtest && sudo apt-get install --no-install-recommends yarn
SCRIPT

$rbenv_script = <<SCRIPT
if [ ! -d ~/.rbenv ]; then
  git clone https://github.com/rbenv/rbenv.git ~/.rbenv
  echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
  echo 'eval "$(rbenv init -)"' >> ~/.bashrc
fi
if [ ! -d ~/.rbenv/plugins/ruby-build ]; then
  git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
  echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
fi
export PATH="$HOME/.rbenv/bin:$PATH"
export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"
eval "$(rbenv init -)"
echo $PATH
echo 'installing ruby'
if [ ! -e .rbenv/versions/#{RUBY_V} ]; then
  rbenv install #{RUBY_V}
  rbenv global #{RUBY_V}
fi

#if [ ! -e /home/vagrant/.rbenv/shims/bundle ]; then
  gem install bundler
  rbenv rehash
  gem install rails -v #{RAILS_V}
  gem install mailcatcher

  rbenv rehash
#fi
SCRIPT


$sphinx_script = <<SCRIPT
echo 'Installing sphinx and its dependencies'
sudo apt-get install libmysqlclient20 libodbc1 libpq5 -y
wget http://sphinxsearch.com/files/sphinxsearch_2.2.11-release-1~xenial_amd64.deb
sudo dpkg -i sphinxsearch_2.2.11-release-1~xenial_amd64.deb
echo 'libraries for nokogiri to work'
#sudo apt-get install libxslt-dev libxml2-dev -y
#echo 'avoid failure with mysql libmysqlclient-dev -y'
#sudo apt-get install libmysqlclient-dev -y
SCRIPT



Vagrant.configure("2") do |config|
  
  config.vm.define :ruby do |deploy_config|
    #deploy_config.vm.box = "bento/ubuntu-18.04"
    #config.vm.box_version = "201812.27.0"
    #deploy_config.vm.hostname = "deploy"
        
    deploy_config.vm.box = "generic/ubuntu1804"
    deploy_config.vm.hostname = "ubuntu-01"
    deploy_config.vm.box_check_update = false
    

    deploy_config.vm.network "forwarded_port", guest: 3000, host: 3111

    deploy_config.ssh.forward_agent = true
    deploy_config.vm.provision :shell, inline: $apt_script
    deploy_config.vm.provision :shell, privileged: false, inline: $rbenv_script
    deploy_config.vm.provision :shell, privileged: false, inline: $node_script
    deploy_config.vm.provision :shell,  inline: $mysql_script
    deploy_config.vm.provision :shell,  inline: $sphinx_script
    deploy_config.vm.provision :shell, inline: $yarn_script


  end
end