STNS sample (debian jessie)
===

sample playbook for STNS on Debian jessie.

[STNS](http://stns.jp/) is simple user and publickey manage tool.

## Goal

you can ssh with 'stns' user without creation 'stns' linux user on STNS client machine.

## This sample overview

provisioning STNS server and client with ansible.

```
+---------+         +-------------+
| ansible | ---+--> | STNS server |
+---------+    |    +-------------+
               |
               |    +-------------+
               +--> | STNS client |
                    +-------------+
```

## Turtrial

### Tool

- vagrant (with VirtualBox)

### Clone this repository

```
git clone https://github.com/reiki4040/STNS_sample.git
cd STNS_sample
```

### Launch VM

launch 3 VMs

- ansible
- stns_server
- stns_client

```
vagrant up
```

### Copy private keys

copy private keys to ansible server.
those use in ansible-playbook to connect to stns_server and stns_client

```
# set ssh config for connect to ansible server (for next scp)
vagrant ssh-config ansible >> ~/.ssh/config

# copy private key (vagrant generated) to ansible server
scp .vagrant/machines/stns_server/virtualbox/private_key vagrant@ansible:/vagrant/stns_server_private_key
scp .vagrant/machines/stns_client/virtualbox/private_key vagrant@ansible:/vagrant/stns_client_private_key
```

### Setup ansible

connect to ansible server and setup ansible command.

```
# connect to ansible server
vagrant ssh ansible
```

```
## on ansible server ##

# setup ansible
sudo apt-get install -y python-pip build-essential libssl-dev libffi-dev python-dev sshpass
sudo pip install -U ansible markupsafe setuptools cryptography

# check version above 2.x
ansible --version
```

### Add ssh config

add ssh config that connect to stns_server and stns_client.

```
## on ansible server ##

echo '
Host 192.168.20.5
  HostName 192.168.20.5
  User vagrant
  Port 22
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile "/vagrant/stns_server_private_key"

Host 192.168.20.6
  HostName 192.168.20.6
  User vagrant
  Port 22
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile "/vagrant/stns_client_private_key"
' >> ~/.ssh/config
```

### Generate ssh key and try ssh

```
## on ansible server ##

# generate new private key for stns user
ssh-keygen -t rsa -b 4096 -C 'stns sample'
  # no pass phrase (please enter 3 times)

# try ssh
ssh stns@192.168.20.6 -i ~/.ssh/id_rsa
```

but of course failed :D

```
Warning: Permanently added '192.168.20.6' (ECDSA) to the list of known hosts.
Permission denied (publickey,password).
```

### Setup STNS and client with ansible-playbook

run ansible-playbook, with passward 'vagrant'.
vagrant mounted repository directory on /vagrant.

```
## on ansible server ##

# cd to mount directory
cd /vagrant
ls
  # roles/ local/ inventory ... other files.

# run ansible for stns server
ansible-playbook -i local/inventory stns_server.yml -e "ssh_user='stns' public_key='$(head -1 ~/.ssh/id_rsa.pub)'" -k
  # ssh password: vagrant

# run ansible for stns client
ansible-playbook -i local/inventory stns_client.yml -k
  # ssh password: vagrant
```

### Try ssh

try ssh with stns user again! :)

```
## on ansible server ##

# ssh to STNS client
ssh stns@192.168.20.6 -i ~/.ssh/id_rsa
  # exit

# ssh to STNS server and client
ssh stns@192.168.20.5 -i ~/.ssh/id_rsa
  # exit
```

## Something

- not yet create user directory when login
- not yet sudo password turtrial
- nscd is not used in this sample. (nscd has not been good work my environment...)

## Refs

- [STNS](http://stns.jp/)
