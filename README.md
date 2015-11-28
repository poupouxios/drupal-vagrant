# Drupal-vagrant

Drupal Vagrant is a sample project of how to use [Drupal Automation bash scripts](https://github.com/poupouxios/drupal-automation) along with [Vagrant](https://www.vagrantup.com/) and [Ansible](http://www.ansible.com/) to setup in less than 5 minutes  a local Drupal site.

This allows you to setup a Drupal 7 or 8.0 or 8.1 without the hassle of having to setup vhsosts, setup databases, downloading and unzipping the Drupal Core files, setting up the correct permissions so Drupal will work etc.

The project is tested in Ubuntu 14.04.

## Dependencies

The project needs to have some essential components to start which are:

* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Vagrant](https://www.vagrantup.com/). Use this [Getting Started guideline](https://docs.vagrantup.com/v2/getting-started/) to familiarise yourself 
* [Ansible](http://www.ansible.com/)
* [NFS enable on Linux](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-14-04) - This is essential as it decreases the response time when you click around the Drupal site.
* [ShadowKoBolt.php5-common](https://github.com/ShadowKoBolt/ansible-php5-common) - You can install this ansible role by typing `ansible-galaxy install ShadowKoBolt.php5-common`
* [ShadowKoBolt.core](https://github.com/ShadowKoBolt/ansible-core) - You can install this ansible role by executing this command `ansible-galaxy install ShadowKoBolt.core`
* [geerlingguy.ruby](https://github.com/geerlingguy/ansible-role-ruby) -  You can install this ansible role by executing this command `ansible-galary install geerlingguy.ruby`

## Run this sample project

1. Clone the project and put it in any place on your computer `git clone https://github.com/poupouxios/drupal-vagrant.git`
2. Navigate inside the project and execute the below commands:
  * `git submodule init`
  * `git submodule update`
3. When you execute the above commands, the drupal-automation folder should have the latest code
4. Assuming that you installed all the dependecies above, navigate to the base of the folder run `vagrant up`
5. It will prompt you to select what version of Drupal you want to install. The message will look like `Which version of Drupal you want to install? (7 or 8.0 or 8.1): `. Type any of the 3 versions of Drupal.
6. Now it will download the specific version of Drupal you selected from the Drupal github.
6. After that, it will ask you to enter your sudo password. This is because it will try to setup the NFS.
7. Now sit back, grab a coffee or tea and watch the magic of Vagrant and ansible setting up a Virtual Machine and setup everything to have a fresh Drupal site to play.
8. If everything goes fine, and the ansible script finishes successfully, you should be able to access the Drupal site via [http://192.168.50.130](http://192.168.50.130).
9. You should be able also to access [Mailcatcher](http://mailcatcher.me/) through [http://192.168.50.130:1080](http://192.168.50.130:1080)

## Structure of the folders

The scripts will create the below structured folders:
  * `core_structure` folder:  This will have all the Drupal core files downloaded from github
  * `db` folder: This will be an empty folder. This folder will be used to put your MySql dump files in order to load them next time you want to provision your VM and you don't want to setup a fresh Drupal instance
  * `public` folder: This is where your Drupal code will live. This folder has 2 subfolders inside:
    * `current` folder: This folder is the one used outside of the VM to put your code. If you open it you will see a lot of files and folders symlinked from the `core_structure` folder except the sites folders on Drupal 7 where is being created. For Drupal 7 put your code inside the `public/current/sites` folder and for Drupal 8 put your code inside the `public/current` folder.
    * `vagrant` folder: In this folder, don't add any other files. Its purpose is for the VM to be able to access the Drupal core files from `core_structure` folder and the `sites` folder from `public/current`. Any changes you add inside the `public/current/sites` folder, they will automatically appears inside the `vagrant/sites` folder.

## Configure the scripts and setup your own project

### 1. Configuration variables

In order to create your own project, there are some configuration variables that can be set. These variables exist insibe the `playbook.yml` file and are:
  * `root_user`: This is the root username that will be used to set the Drupal admin username and set the MySql username. The password is set by default as `password`
  * `database_name`: The variable describes its purpose. It sets up the database name
  * `host_filename`: This is the name of the host file. In our case the actual file name is sampletest_host, so the variable will be sampletest.
  * `site_name`: This sets the Drupal Site name
  * `drupal_automation_folder`: drupal-automation (Leave this folder name if you didn't change the submodule folder)
  * `database_dump_name`: The purpose of this variable is to set a MySql dump name and ignore the steps of setting up a new Drupal instance. By setting this variable, the scripts will try to find this dump name inside the db folder. An example is to set `drupal7-dump` name and inside db folder you have the `drupal7-dump.sql` file

There are also inside the `Vagrantfile` some configuration variables you might want to change. These are:
  * `config.vm.hostname`: Change the name of this hostname to be something unique for your project
  * `config.vm.network :private_network`: This needs to be changed and each project has its own ip so you can run multiple projects in VM and you can access them at the same time.
  * `vb.customize ['modifyvm', :id, '--cpus', '2', '--memory', 3096]`: This customization is if you want to change the number of cores and memory that will run Drupal inside the VM. I set them for 2 cores and 3GB ram.

## 2. Setup your own project

1. Copy `mailcatcher.yml`,`playbook.yml`,`sampletest_host` and `Vagrantfile` in a folder of you choice. That folder will be your project folder
2. Setup the configuration variables based on your project needs
3. `git init` inside your project folder
4. `git submodule add https://github.com/poupouxios/drupal-automation.git`
5. `vagrant up` to start the VM
6.  Select the version of Drupal
7.  That's it! Now wait until the scripts finish.

## Extra components

The only extra component that is added in the `playboook.yml` file is Mailcatcher. Mailcatcher offers the ability to catch emails that are being sent through Drupal so you can debug the content of the email, check email headers or avoid accidentally sending emails to real people (As a best practise, you must always avoid testing with real email of clients. Always create fake accounts on development environments).

## Extra useful information

1. Assumed that you have already setup locally a Drupal version and you want to switch to this automatic way. Just get a MySql dump of your project, put it inside the `db` folder, set your `database_dump_name` variable and load the VM. After that, copy paste your code in the correct folders (inside `public/current` folder) and then run Vagrant.
2. The first time Vagrant will run, it will provision to setup or load from a MySql dump Drupal. After that, it will only execute the first part to boot up the VM. If you want to force it to provision after the `vagrant up` command execute `vagrant provision`.
3. Always remember to `vagrant halt` before you shut down your PC because Linux don't shut down the VMs automatically and it hangs in the shut down process which leaves you with the option to press the shut down button on your computer to close.
4. If for somehow the VM was misconfigured and you want to run a new one, just execute `vagrant destroy` and then `vagrant up`. The destroy command will remove entirely the VM and it will start a fresh one. **Always before destroy, ensure that you made a database dump because after the VM is destoyed anything inside the VM will be deleted. The files will not be deleted as they exist outside of the VM**
5. In order to get inside the VM, navigate to your project folder and execute `vagrant ssh`. This will get you inside the VM in order to access your MySql or configurate some OS stuff etc.

## TODO List

Some future things to be added are:
 * Use capistrano to automate deployments on your server
 * Add more extra components

## License

This project is licensed under the MIT open source license.

## About the Author

[Valentinos Papasavvas](http://www.papasavvas.me/) works as a Senior Web Developer and iOS Developer in a company based in Sheffield/UK. You can find more on his [website](http://www.papasavvas.me/).
