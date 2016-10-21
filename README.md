# Ansible - LEMP on Ubuntu

This ansible playbook will provision servers with a full LEMP stack   
and more specifically to run PHP based apps.

Thre will be an optional extra soon to include;

- Node
- Mysql

<br>

## TL;DR

Install shit on your machine - [VirtualBox](https://www.virtualbox.org/wiki/Downloads), [Vagrant](https://www.vagrantup.com/downloads.html), [Ansible](http://docs.ansible.com/ansible/intro_installation.html#latest-releases-on-mac-osx)

Install Ansible on the server

```bash
sudo apt-get update && sudo apt-get install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible && sudo apt-get update
sudo apt-get install ansible python-apt aptitude -y
```

Edit to match requirements `group_vars/all/main.yml`

Duplicate and fill out necessary server ip addresses `hosts.orig > hosts`

Generate/save a password using 1password and place it in a `.vault_pass` file

Edit then encrypt the `group_vars/all/vault.yml` file.

provision that shit `ansible-playbook`

<br>

## Ansible-Vault & Security

Sensitive files can be a pain but Ansible makes this a breeze.

Begin by copying the `vault.example.yml` template in `group_vars/all/` to `vault.yml`.

Once you've done that, open it up and place all your sensitive information here passwords, API keys, tokens, etc.

#### Things to note:

1. All variables must be prefixed with a `vault_` prefix
2. Files should not be committed to version control, they are highly sensitive
   and will give away sever login details
3. You can add a further security measure for passwords here by creating a SHA512 (see below)
4. in the root of the playbook you can create a `.vault_pass` (see below)
5. You need to remember to encrypt the file using the `ansible_vault` command

#### Create SHA512

Sometimes even though the file will be encrytped you might want a further
layer of security.

Visit the following link:  
http://docs.ansible.com/ansible/faq.html#how-do-i-generate-crypted-passwords-for-the-user-module

#### Vault Pass

The `.vault_pass` file is a quick way for you to edit the `vault.yml`
without having to type the password each time you want to do so.

Create a `.vault_pass` file in the root of the playbook which is just one line
containing the password, so if your password is `password134` (never use password as your password)
you would just simply put that.

#### Encrypting vault.yml

To encrypt the vault file, assuming you have a `.vault_pass` file is as simple
as running the following command (from the root of your playbook).  This will use the password in .vault_pass so you don't have to worry about it!

`ansible_vault encrypt group_vars/all/vault.yml`

This will encrypt the file and if you try to edit it, it will just appear to be jargon.

To edit the file you run

`ansible_vault edit group_vars/all/vault/yml`

A good habbit to get into is to not unencrypt the file as you may forget and leave this open for anyone to view/edit

<br>

## Generate Your SSH keys

Next you need to generate SSH keys to place in your `provision.yml` file.

These will be used to connect to your server as the appuser/appadmin and allow
the server to talk to github to pull down/interact with your repo.

You can use the below to generate your ssh keys

```bash
# Required for ansible to talk to login through SSH and run commands
ssh-keygen -t rsa -b 4096 -C "example@example.com" -f ~/.ssh/id_testapp

# Required in order to authenticate with github
ssh-keygen -t rsa -b 4096 -C "example@example.com" -f ~/.ssh/id_testapp_github
```

<br>

Once you've done this, place the raw public key files into the `group_vars/all/main.yml` file
and the easiest way to do this is.

```bash
# Take the contents of the public SSH key and place into your clipboard
$ cat ~/.ssh/id_testapp.pub | pbcopy

# Now just paste where appropriate!
```
<br>

## Running on virtual machine/host

You don't need to login to your VM in order to run Ansible as we 
use the `ansible provisioner` provided by Vagrant which allows us 
to install it on `vagrant up`.

A Vagrantfile is also included in the project so you can just place this in the same directory and everything should be good to go.

```bash
# First time installation
$ vagrant up

# If you need to re-provision after altering config
$ vagrant reload --provision
```

<br>

## Ansible.cfg

There is an additional config file but you will most likely never need to touch this, but further info for configuring this can be found in the [ansible docs](http://docs.ansible.com/ansible/intro_configuration.html).

**Never** Much like the vault section above never add this to your VCS.

<br>

## Hosts file

In the playbook root there is a `hosts.orig` file, copy this and name it `hosts`.  When you open the file the defaults are for the VM if you want to spin up a VM with the playbooks.

However you'll most likely want to add a server(s) that you want to provision so go ahead and add the IP address under the all/web group.

Once you've done that you should be good here.

<br>

## Provisioning - provision.yml

This is the entry point for the playbook when you tell ansible to begin execution.  In this file the seperate provisioning blocks are setup already with the tasks that need to be run and in what order, so you shouldn't need to edit this file.

By default everything will get executed when running this playbook with the following:

`ansible-playbook --private-key ~/.ssh/id_testapp -i hosts provision.yml` 

If for some reason you don't want to run everything in the playbook you can specify your own series of hosts to run by using something like the following

<br>

## Running Ansible

```bash
cd ~/path/to/playbook-dir

# Check our Yaml Syntax
ansible-playbook --private-key=~/.ssh/id_testapp \
-i hosts \
--syntax-check \
provision.yml

# Run Everything!
ansible-playbook --private-key=~/.ssh/.pem \
-i hosts \
provision.yml

# Or, Limit runs by host
# In this example, we run only load_balancer tasks
ansible-playbook --private-key=~/.ssh/fideloperllc.pem \
-i hosts --limit=load_balancer \
provision.yml
```

