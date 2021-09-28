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
- Python v. 3.8.6
- VirtualBox Version 6.1.14
- Ubuntu Server 20.10 (64bit) virtual disk image from [osboxes](https://www.osboxes.org/)

## Steps

### Prerequisites

1. [Create a bot in Telegram](https://core.telegram.org/bots#:~:text=for%20existing%20ones.-,Creating%20a%20new%20bot,mentions%20and%20t.me%20links.) and save its token.
2. Create a PostgreSQL database. Create objects using [SQL scripts](https://github.com/ihnashchenka/TelegramBot/tree/master/db/sql).
3. Put some data into the db. You can use [this .csv](https://github.com/ihnashchenka/TelegramBot/blob/master/db/data/music.csv) as a initial source.

### Create ansible host

Usually Windows machine can't be used as a  Ansible host. However it is possible to enable Windows subsystem Linux.

4. Enable WSL and install Ubuntu application from the Store. [Instaruction]((https://www.ssl.com/how-to/enable-linux-subsystem-install-ubuntu-windows-10/). 
5. Check that ssh is availible on the host.
6. Install Ansible `sudo apt-get install ansible`.

Files from the Windows machine are visible from the command line. \
Pathes are transformed in the following way: \
`C:\\some\windows\path\file.txt` --> `/mnt/c/some/windows/path/file.txt`

### Target host

7. Create a virtual machine with [Ubuntu Server 20.10](https://www.osboxes.org/ubuntu-server/#ubuntu-server-20-10-vbox).
8. Configure ports forwarding on the virtual machine:

| Name          |Host port    | Guest port      | Comment                              |
| ------------- |:-----------:|:---------------:|:------------------------------------:|
| SSH           | 2222        | 22              | Used by Ansible to connect to the VM |
| HTTPS         | 4444        | 443             | Used to connect to the Telegram API  |

9. Start the virtual mahine.

Once I created a virtual machine using the osboxes' .vdi file, it already have python3 and git installed. It can be checked using: \
`git --version` \
 `python --version` or `python3 --version` 
 
10.  [Install open-ssh server] on virtual machine(https://help.skytap.com/connect-to-a-linux-vm-with-ssh.html). 

11. Test the connection between the ansible host and virtual machine. In ansible host terminal on Windows run: \
`ssh 127.0.0.1 -p 2222`

Initially I have problem with `apt update' on the virtual machine. [Fix](https://stackoverflow.com/questions/64120030/hash-sum-mismatch-when-apt-get-update-ubuntu-20-04-vm-with-multipass) it before running the playbook.

### Run playbook

All actions should be done in the ansible host terminal.

12. Clone this git repotitory.
13. Clean *api_config.yml* file. Put there secret values in the following format: \
`TELEGRAM_TOKEN:<your_bot_token>  
DATABASE_URL:postgres://<user>:<password>@<db_host>:<port>/<db_name> `
14. Navigate to `/transformed_path_to_cloned_repo/palybooks/vars`
15. Run `sudo apt-get install sshpass`
16. Encrypt *api_config.yml*: \
`ansible-vault create api_config.yml` \
It will ask to enter a password to encript the file. Remember it, it is used later on to run the playbook.
17. Navigate to `/transfomed_path_to_cloned_repo/`
18. Run the playbook: \
`ansible-playbook -i inventory -K playbooks/install.yml --ask-vault-pass` \
Enter the root user password. \ 
Enter the vault password(the one used in point 16).

**In case the playbook finished successfully, try to communicate with the bot via Telegram!**

<span style="font-family:Papyrus; font-size:2em;">
 https://stackoverflow.com/questions/64120030/hash-sum-mismatch-when-apt-get-update-ubuntu-20-04-vm-with-multipass
Problem with pip install hash
rm ~/.cache/pip -rf
</span>
