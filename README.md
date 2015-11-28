# Drupal-vagrant

Drupal Vagrant is a sample project of how to use [Drupal Automation bash scripts](https://github.com/poupouxios/drupal-automation) along with [Vagrant](https://www.vagrantup.com/) and [Ansible](http://www.ansible.com/) to setup in less than 5 minutes  a local Drupal site.

This allows you to setup a Drupal 7 or 8.0 or 8.1 without the hassle of having to setup vhsosts, setup databases, downloading and unzipping the Drupal Core files, setting up the correct permissions so Drupal will work etc.

The project is tested in Ubuntu 14.04.

## Dependencies

This project needs to have some essential components to start which are:

* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Vagrant](https://www.vagrantup.com/)
* [Ansible](http://www.ansible.com/)
* [NFS enable on Linux](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-14-04) - This is essential as it decreases the response time when you click around the Drupal site.
* [ShadowKoBolt.php5-common](https://github.com/ShadowKoBolt/ansible-php5-common) - You can install this ansible role by typing `ansible-galaxy install ShadowKoBolt.php5-common`
* [ShadowKoBolt.core](https://github.com/ShadowKoBolt/ansible-core) - You can install this ansible role by executing this command `ansible-galaxy install ShadowKoBolt.core`
* [geerlingguy.ruby](https://github.com/geerlingguy/ansible-role-ruby) -  You can install this ansible role by executing this command `ansible-galary install geerlingguy.ruby`

## Run this sample project

1. Clone this project and put it in any place on your computer `git clone https://github.com/poupouxios/drupal-vagrant.git`
2. Navigate inside the project and execute the below commands:
  * `git submodule init`
  * `git submodule update`
3. When you execute the above commands, the drupal-automation folder should have the latest code
4. Assuming that you installed all the dependecies above, navigate to the base of the folder run `vagrant up`
5. It will prompt you to select what version of Drupal you want to install. The message will look like `Which version of Drupal you want to install? (7 or 8.0 or 8.1): `. Type any of the 3 versions of Drupal.
6. Now it will download the specific version of Drupal you selected from the Drupal github.
6. After that, it will ask you to enter your sudo password. This is because it will try to setup the NFS.
7. Now sit back, grab a coffee or tea and watch the magic of Vagrant and ansible setting up a Virtual Machine and setup everything to have a fresh Drupal site to play.
8. If everything goes fine, and the ansible script finishes successfully, you should be able to access the Drupal site via `http://192.168.50.130`.

## Configure the scripts and setup your own project

### 1. Configuration variables

In order to create your own project, there are some configuration variables that can be set. These variables exist insibe the `playbook.ynl` file and are:
  * `root_user`: This is the root username that will be used to set the Drupal admin username and set the MySql username
  * `database_name`: The variable describes its purpose. It sets up the database name
  * `host_filename`: This is the name of the host file. In our case the actual file name is sampletest_host, so the variable will be sampletest.
  * `site_name`: This sets the Drupal Site name
  * `drupal_automation_folder`: drupal-automation (Leave this folder name if you didn't change the submodule folder)
  * `database_dump_name`: The purpose of this variable is to set a MySql dump name and ignore the steps of setting up a new Drupal instance. By setting this variable, the scripts will try to find this dump name inside the db folder. An example is to set `drupal7-dump` name and inside db folder you have the `drupal7-dump.sql` file

There are also inside the `Vagrantfile` some configuration variables you might want to change. These are:
  * `config.vm.hostname`: Change the name of this hostname to be something unique for your project
  * `config.vm.network :private_network`: This needs to be changed and each project has its own ip so you can run multiple projects in VM and you can access them at the same time.
  * `vb.customize ['modifyvm', :id, '--cpus', '2', '--memory', 3096]`: This customization is if you want to change the number of cores and memory that will run Drupal inside the VM. I set them for 2 cores and 3GB ram.
