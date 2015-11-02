# -*- mode: ruby -*-
# vi: set ft=ruby :

### Using Vagrant to set up your dev environment
#
# 1. Install Vagrant
# 2. Install Virtualbox
# 3. From this directory, run `vagrant up` to create and provision the virtual machine.
# 4. Run `vagrant ssh` to ssh into the VM.
# 5. cd to `/vagrant`, which is where this source code directory is mounted into the VM.
# 6. Run `npm install` to install dependencies linked against the VM's native libraries.
#
# Now you can run tests with `npm test`.  Any time you want to use the VM,
# run `vagrant up ; vagrant ssh` to connect.

Vagrant.configure(2) do |config|

  # Create an Ubuntu VM
  config.vm.box = "ubuntu/trusty64"

  config.vm.provision "shell", privileged: false, inline: <<-SHELL

    ### upgrade already-installed packages
    sudo apt-get update

    ### Install MySQL, PostgreSQL, and other dependencies

    # Set a password to avoid interactive password prompt during installation
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password asdf'
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password asdf'

    sudo apt-get -y install mysql-server mysql-client postgresql postgresql-client g++

    ### Set up MySQL
    # Disable root password
    mysql -u root -pasdf -e "SET PASSWORD FOR root@localhost=PASSWORD('');"
    # Create database for bookshelf test suite
    mysql -u root -e "CREATE DATABASE bookshelf_test;"

    ### Set up PostgreSQL
    sudo sed -i '1s/^/host all all 127.0.0.1\/32 trust\\n/' /etc/postgresql/*/main/pg_hba.conf
    sudo service postgresql restart
    psql -h 127.0.0.1 -U postgres << END
        CREATE DATABASE bookshelf_test;
END

    ### Install nvm
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.29.0/install.sh | bash
    eval "`cat ~/.bashrc`"
    nvm install stable
    # LTS version
    nvm install 4.2.1
    nvm use stable
    nvm alias default stable

  SHELL
end
