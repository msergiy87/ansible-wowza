# ansible-wowza

## Ansible role for Wowza 4.4.1
This role automates the installation of the Wowza Media Server, including their installer, which contains a very
long EULA, and 7 interactive prompts.

# Requirements
- Java jdk - I use openjdk.
- Wowza license - this you will get when you sign up for a free trial, or you can request a developer license.
- Wowza binary - the Wowza binary (4.4.1) is now wgetted as part of this role. 

# Defaults

```
wowza_file: "WowzaStreamingEngine-4.4.1-linux-x64-installer.run"
wowza_download_path: "www.wowza.com/downloads/WowzaStreamingEngine-4-4-1"
user_name: "admin"
password: "admin"
license_key: "insert key between quotations"
```

# Distros tested
Currently, this is only tested on Ubuntu 14.04 as a client and server machine. It should theoretically work on older versions of Ubuntu or Debian based systems.

## Wait a minute, what the hell is Wowza?
From the Wowza site:

_Wowza Streaming Engine™ is robust, customizable media server software that powers reliable streaming of high-quality video and audio to any device, anywhere._

_Wowza software is platform-agnostic, multi-format, and multi-screen. It takes in any video format, transcodes it once, and reliably delivers it in multiple formats and with the highest possible quality_

## Usage

##### 1) Install Ansible.

```shell
apt-get update
apt-get install git -y
apt-get install software-properties-common

apt-add-repository ppa:ansible/ansible

apt-get update
apt-get install ansible
```

##### 2) Prepare host. Copy `id_rsa.pub` key from desktop to server `/root/.ssh/authorized_keys`.
```shell
cat /root/.ssh/id_rsa.pub

mkdir /root/.ssh
vim  /root/.ssh/authorized_keys
```

##### 3) Clone repository.

```shell
mkdir ~/my_deploy_code
cd ~/my_deploy_code
git clone https://github.com/msergiy87/ansible-wowza.git
```

##### 4) Create `web.yml` file with content.

```
vim web.yml

---
- hosts: all
  user: root

  roles:
    - ansible-wowza
  tasks:
    - debug: msg={{ansible_default_ipv4}}
```

##### 5) Create `hosts` file.

```shell
vim hosts
192.168.0.103
```

##### 6) Add license key and change username and password.

```shell
vim ansible-wowza/defaults/main.yml

user_name: "admin"
password: "admin"
license_key: "insert key between quotations"
```

##### 7) Run install process.

```shell
ansible-playbook -i hosts web.yml
```

##### 8) Run web interface.
```
http://192.168.0.103:8088/enginemanager
```

Wowza needs several user actions on the interactive prompt portion of the installer:
- an acknowledgement of acceptance of their terms
- an administrative user added for using the Wowza console
- a password for the administrative user
- confirmation of that password
- Wowza license key
- an acknowledgement of whether or not you want Wowza to start at boot

Fill these values in, in vars/main.yml. I've already taken the liberty of answering "yes" for the acceptance of terms,
and whether or not you want Wowza to start at boot, but you can easily change that, should you feel the need. Those 
values can be edited in the template/script.exp.j2 file.
