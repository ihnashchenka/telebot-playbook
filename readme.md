# First steps in Ansible. Deploy TelegramBot GuessMu 3.0 on Ubuntu server

## Backhistory

This repo was created to make a first step with Ansible.
Some time ago I have created a Telegram bot called GuessMU 3.0. It is written in Python. You can check its code in my [TelegramBot repository](https://github.com/ihnashchenka/TelegramBot).

The application interacts with a user via TelegramBotAPI and stores internal data inside a PostgeSQL database.
Both bot and db are hosted on Heroku platform(Ubuntu env and PostgeSQL add-on). All code changes are automatically deployed on the Heroku application via internal pipeline triggered by a push to master branch.

I decided to use this application as a first exercise with Ansible as there are something to download to a server, update pip packages, start the application and check its logs.

## Objective

Create an Ansible host on a Windows laptop. Deploy GuessMu 3.0 to an empty Ubuntu server using Ansible script. Start the bot and connect it to the existing PostgreSQL.

## Technical info about GuessMu 3.0

- Started by running [bot.py](https://github.com/ihnashchenka/TelegramBot/blob/master/bot.py).
- Rquires Python packages specified in [requirements.txt](https://github.com/ihnashchenka/TelegramBot/blob/master/requirements.txt).
- Database link and auth information are written to the `DATABASE_URL` env variable.
- Must be regester in Telegram and have a token to communicate with API. Token is written to the  `TELEGRAM_TOKEN` env variable.
- There is a webhook set up on Heroku platform to monitor incoming messages. In this task the bot will be started in polling mode instead.

## Set up
- Windows 10 Home v.21H1
- Python v.3.8.6
- VirtualBox v.6.1.14
- Ubuntu Server 20.10 (64bit) virtual disk image from [osboxes](https://www.osboxes.org/)

## Steps

### Prerequisites

1. [Create a bot in Telegram](https://core.telegram.org/bots#:~:text=for%20existing%20ones.-,Creating%20a%20new%20bot,mentions%20and%20t.me%20links.) and save its token.
2. Create a PostgreSQL database. Create objects using [SQL scripts](https://github.com/ihnashchenka/TelegramBot/tree/master/db/sql).
3. Put some data into the db. Use [this .csv](https://github.com/ihnashchenka/TelegramBot/blob/master/db/data/music.csv) as a initial source.

### Create ansible host

Usually Windows machine can't be used as a  Ansible host. However it is possible to enable Windows subsystem for Linux.

4. Enable WSL and install Ubuntu application from the Microsoft Store. [Instruction](https://www.ssl.com/how-to/enable-linux-subsystem-install-ubuntu-windows-10/). 
5. Open Ubuntu application.
6. In opened terminal check that ssh is availible on the host.
7. Install Ansible. Run the following command in Ubuntu terminal:
 `sudo apt-get install ansible`.

Files from the Windows machine are visible from the command line. \
Pathes are transformed in the following way: \
`C:\\some\windows\path\file.txt` --> `/mnt/c/some/windows/path/file.txt`

### Target host

8. Create a virtual machine in VirtualBox with [Ubuntu Server 20.10](https://www.osboxes.org/ubuntu-server/#ubuntu-server-20-10-vbox) inside.
9. Configure ports forwarding on the virtual machine:

| Name          |Host port    | Guest port      | Comment                              |
| ------------- |:-----------:|:---------------:|:------------------------------------:|
| SSH           | 2222        | 22              | Used by Ansible to connect to the VM |
| HTTPS         | 4444        | 443             | Used to connect to the Telegram API  |

10. Start the virtual machine.

Once I created a virtual machine using the osboxes' .vdi file, it already had python3 and git installed. It can be checked using: \
`git --version` \
 `python --version` or `python3 --version` 
 
11.  [Install open-ssh server](https://help.skytap.com/connect-to-a-linux-vm-with-ssh.html) on the virtual machine
12. Test the connection between the ansible host and virtual machine. In ansible host terminal on Windows run: \
`ssh 127.0.0.1 -p 2222`

Initially I had problem with `apt update` on the virtual machine. [Fix](https://stackoverflow.com/questions/64120030/hash-sum-mismatch-when-apt-get-update-ubuntu-20-04-vm-with-multipass) it before running the playbook.

### Run playbook

All below actions should be done in the ansible host terminal.

13. Clone this git repotitory.
14. Clean *api_config.yml* file. Put there secret values in the following format:
```
TELEGRAM_TOKEN:<your_bot_token>  
DATABASE_URL:postgres://<user>:<password>@<db_host>:<port>/<db_name>  
```
15. Navigate to */transformed_path_to_cloned_repo/palybooks/vars*
16. Run `sudo apt-get install sshpass`
17. Encrypt *api_config.yml*: \
`ansible-vault create api_config.yml` \
It will ask to enter a password to encript the file. Remember it, it is used later on to run the playbook.
18. Navigate to */transfomed_path_to_cloned_repo/*
19. Run the playbook: \
`ansible-playbook -i inventory -K playbooks/install.yml --ask-vault-pass` \
Enter the root user password. \
Enter the vault password(the one used in point 16).

**In case the playbook finished successfully, try to communicate with the bot via Telegram!**

*
https://stackoverflow.com/questions/64120030/hash-sum-mismatch-when-apt-get-update-ubuntu-20-04-vm-with-multipass
Problem with pip install hash
rm ~/.cache/pip -rf
*
