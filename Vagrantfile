system("
    if [ #{ARGV[0]} = 'up' ]; then
        echo '--------------------------------------------------------\n'
				echo 'INITIALIZING DRUPAL CORE FILES BEFORE VAGRANT PROVISIONS\n'
				echo '--------------------------------------------------------\n'
        ./drupal-automation/initialize_drupal_structure.sh
    fi
")

Vagrant.configure('2') do |config|
  config.vm.box = 'bento/debian-8.2'

  config.vm.hostname = 'drupal-vagrant'
  config.vm.network :private_network, ip: '192.168.50.130'
  config.vm.network "forwarded_port", guest: 1080, host: 8080 
  config.vm.network "forwarded_port", guest: 1025, host: 8025 

  config.vm.synced_folder '~', '/home/master'
  config.vm.synced_folder '.', '/home/vagrant/source', type: "nfs"
  config.vm.synced_folder '.', '/vagrant'

  config.vm.provider :virtualbox do |vb|
    vb.customize ['modifyvm', :id, '--cpus', '2', '--memory', 3096]
  end

	config.vm.provision "shell", path: "drupal-automation/setup_vagrant_files.sh"

  config.vm.provision :ansible do |ansible|
    ansible.playbook = 'playbook.yml'
  end
end
