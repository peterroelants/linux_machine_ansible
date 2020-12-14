# Configure laptop with ansible

This repo contains a proof-of-concept of how to configure machines with ansible. For now it can configure Peter's laptop. While I wouldn't recommend to run it on your machine out-of-the box it can be a template that can help you configure your machine with minimal changes. In the future, once we have to maintain multiple company machines, this can be used to standardise and maintain those machines.


## Ansible

[Ansible](https://docs.ansible.com/ansible/latest/user_guide/index.html#getting-started) is a declarative configuration management tool allowing you to configure a set of machines (laptops, servers, VMs, ...). The state of the machines-to-configure is written down in yml files and configuration can be run remotely over ssh as well as local.

Sources:
- [Ansible getting started](https://docs.ansible.com/ansible/latest/user_guide/index.html#getting-started)
- [Introduction on how to configure a MacBook with ansible](https://blog.laurentcharignon.com/post/laptop-setup-with-ansible/)
- [Short overview of ansible](https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide)
- [Ansible Playbooks intro](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html)


### Setup Ansible

Install ansible:
Via Python:
```
sudo apt update --yes
sudo apt install --yes python3 python3-pip
pip3 install ansible jinja2 --user
```

or aptitude:
```
sudo apt update && sudo apt install ansible
```

## Run this repo

This repo is intentend to run on the local host without a remote connection, as is defined in the `./inventory` file.

The different [roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) in `./roles` define how to setup various apps and configs. These are selected an run by the `./local_peter.yml` [playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html) that can be used to configure Peter's laptop.


### Run The Ansible Playbook
Run `local_peter.yml` on Peter's freshly installed ubuntu laptop to set it up:
```
ansible-playbook -verbose -i inventory local_peter.yml -c local --ask-become-pass
```

Here the [`ansible-playbook`](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) command will run `local_peter.yml` with a local connection to the localhost defined in `./inventory`.
`--ask-become-pass` is used to get the sudo password for when downstream setup scripts need this.


### Pre-setup

While the ansible script takes care of running an upgrade, it might be good to run the following in the beginning followed by a reboot:
```
sudo apt update && sudo apt full-upgrade
```


### Post Setup

Since the notebook runs locally rebooting and continuing the script become difficult. There are workarounds, for example by using [cron at startup](- https://www.reddit.com/r/ansible/comments/e8f0lf/rebooting_ansible_host_via_playbook/) however we don't want to make things unesserarily complex. When configuring a whole host of machines via ssh reboots don't become an issue because the main machine running the ansible script can login again once the machine has restarted. Ansible has functionalities to reboot a remote host and wait for it.

#### Reboot

Reboot!

#### Check nvidia

Check Nvidia
```
nvidia-smi
```

Check CUDA:
```
nvcc --version
```

#### Cleanup

```
sudo apt autoremove && sudo apt autoclean
```

#### Update firmware:

```
sudo apt update && sudo apt upgrade -y
sudo service fwupd start
sudo fwupdmgr refresh
sudo fwupdmgr update
```
More info at: https://itsfoss.com/update-firmware-ubuntu/


## Info on Lenovo P1 Gen 3:
- https://download.lenovo.com/pccbbs/mobiles_pdf/p1_gen3_x1extreme_gen3_ug_en.pdf
- https://certification.ubuntu.com/hardware/202009-28224
- https://download.lenovo.com/pccbbs/mobiles_pdf/tp_p1_gen2_ubuntu_18.04_lts_installation_v1.0.pdf
- https://forums.lenovo.com/t5/ThinkPad-P-and-W-Series-Mobile-Workstations/P1-Gen-3-Linux-option/m-p/5034527
