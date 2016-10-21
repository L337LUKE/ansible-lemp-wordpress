# Ansible - LEMP on Ubuntu

This ansible playbook will provision servers with a full LEMP stack   
and more specifically to run PHP based apps.

Thre will be an optional extra soon to include;

- Node
- Mysql

<br>

## TL;DR

Install stuff on your machine:

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) (optional)
- [Vagrant](https://www.vagrantup.com/downloads.html), (optional)
- [Ansible](http://docs.ansible.com/ansible/intro_installation.html#latest-releases-on-mac-osx)

Install Ansible and some other requirements on your servers

```bash
sudo apt-get update
sudo apt-get install software-properties-common -y
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible python-apt aptitude -y
```
Generate SSH keys for ansible to use 
`ssh-keygen -t rsa -b 4096 -C "example@example.com" -f ~/.ssh/id_testapp`

Edit to match requirements `group_vars/all/main.yml`

Create a `.vault_pass` file in the root (store passwords in 1password also)

Copy `vault.yml.default` to `vault.yml` and fill out values

!IMPORTANT!  
Encrypt `vault.yml` before committing this file to your repo otherwise you're basically giving
everyone your passwords.  

I recommend using the `ansible-vault edit group_vars/all/vault.yml` command.
which will never un-encrypt your files

Fill out necessary hosts in the `inventories/` dir

Provision and be happy
```bash
ansible-galaxy install -r requirements.yml
ansible-playbook --key-file=~/.ssh/id_rsa -i inventories/webservers playbook.servers.yml
```
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
```

<br>

Once you've done this place the raw public key file into the  
`group_vars/all/main.yml` file and the easiest way to do this is  
via piping to `pbcopy`.

```bash
$ cat ~/.ssh/id_testapp.pub | pbcopy
```
<br>

## Test/Setup on VM

You can also provision a VM with Ansible if you need and this can be  
done simply on `vagrant up` no hassle and using the `Vagrantfile` in  
this repository.

You can use this as a development environment or to simply just  
test your playbook.

```bash
vagrant up
```

<br>

## Running The plays

```bash
cd ~/path/to/playbook-dir

# Install required galaxy roles
ansible-galaxy install -r requirements.yml

# Provision Servers
ansible-playbook --key-file=~/.ssh/id_rsa -i inventories/webservers playbook.servers.yml

# Limit hosts if you need
ansible-playbook --key-file=~/.ssh/id_rsa -i inventories/webservers playbook.servers.yml --limit=server_group
```
