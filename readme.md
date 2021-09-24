# First steps in Ansible. Deploy TelegramBot GuessMu 3.0 on Ubuntu server

## Backhistory

## Objective
Create a simple Ansible playbook aand run it from Windows host

## Set up
- Windows 10 Home Version 21H1
- VirtualBox Version 6.1.14
- Ubuntu Server 20.10 (64bit) virtual disk image from [osboxes](https://www.osboxes.org/)

## Steps
1) enable linux for win
2) download ubuntu
3) check ssh
4) install ansible
5) download vb
6) download vdi
7) open port 2222 -> 22
8) install open-sss server 
https://help.skytap.com/connect-to-a-linux-vm-with-ssh.html
9) test ssh from local ubuntu
10) fix problem with apt

https://stackoverflow.com/questions/64120030/hash-sum-mismatch-when-apt-get-update-ubuntu-20-04-vm-with-multipass
10) specify user and pwd in inventory
11) sudo apt-get install sshpass
12) go to /mnt/c/
13) run playbook
/mnt/c/users/tanys/Desktop/Study/Ansible/telebot-playbook$ ansible-playbook -i inventory playbooks/helloworld.yml

uk.archive.ubuntu.com 




https://stackoverflow.com/questions/64120030/hash-sum-mismatch-when-apt-get-update-ubuntu-20-04-vm-with-multipass


when a problem with pip install hash

rm ~/.cache/pip -rf


systemctl enable telebot
systemctl start telebot
systemctl status telebot