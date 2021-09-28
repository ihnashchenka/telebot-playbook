# First steps in Ansible. Deploy TelegramBot GuessMu 3.0 on Ubuntu server

## Backhistory

This repo was created in order to make a first step with Ansible.
Some time ago I have create a Telegram bot called GuessMU 3.0. It is written in Python. You can check its code in my [TelegramBot repository](https://github.com/ihnashchenka/TelegramBot)

The application interactes with a user via TelegramBotAPI and stores internal data inside a PostgeSQL database.
Both bot and db are hosted on Heroku platform(Ubuntu env and PostgeSQL add-on). All code changes are automatically deployed on the Heroku application via internal pipeline triggered by a push to master branch.

I decided to use this application as a first exercise with Ansible as there is something to download to a server, update pip packages, start the application and check its logs.

## Objective

Create an Ansible host on a Windows laptop. Deploy GuessMu 3.0 to an empty Ubuntu server using Ansible script. Start the bot and connect it to the existing PostgreSQL.

## Technical info about GuessMu 3.0

- Started by running [bot.py](https://github.com/ihnashchenka/TelegramBot/blob/master/bot.py)
- Rquired Python packages specified in [requirements.txt](https://github.com/ihnashchenka/TelegramBot/blob/master/requirements.txt)
- Database link and auth information written to the `DATABASE_URL` env variable
- Must be regester in Telegram and have a token to communicate with API. Token is written to the  `TELEGRAM_TOKEN` env variable
- There is a webhook setted up on Heroku platform to monitor incoming messages. In this task the bot will be started in polling mode instead.

## Set up
- Windows 10 Home Version 21H1
- VirtualBox Version 6.1.14
- Ubuntu Server 20.10 (64bit) virtual disk image from [osboxes](https://www.osboxes.org/)

## Steps

### Prerequisites

### Create ansible host
1) enable linux for win

### Target host
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

### Run playbook
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
