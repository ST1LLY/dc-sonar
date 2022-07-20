# DC Sonar Community

## Disclaimer

It's only for education purposes

Avoid using it on production AD

Neither contributor incur any responsibility for any using it

## Description

TODO

## Donations for the further development

Bitcoin (BTC): bc1qlymngj29vvknxjl73u3w3u0s84qexpvgac4jhr

Ethereum (ETH): 0x4186c3d5b7B58C761Dd118AffCA1Cf24Ba8A3E89

Bitcoin Cash (BCH): qq5729vd50k3f57rwpnfh8kvhzn0lxurxvfsv4pmq8



## Deployment for development

### Windows host and Ubuntu Server guest

In this case we are going to edit code on Windows host while run Python code on Ubuntu guest

#### Environment

[VirtualBox Version 6.1.34 r150636 (Qt5.6.2)](https://www.virtualbox.org/wiki/Downloads)

[Ubuntu Server 22.04](https://ubuntu.com/download/server)

[Python 3.10](https://www.python.org/downloads/)

#### Preparations

[Create](https://www.virtualbox.org/manual/ch01.html#gui-createvm) virtual machine with 2 CPU, 2048 MB RAM, 10GB  with [Ubuntu Server 22.04](https://ubuntu.com/download/server) iso

if Ubuntu installer asks for updating ubuntu installer before VM's installation - agree

Choose to install OpenSSH Server

VirtualBox Port Forwarding Rules

| Name                        | Protocol | Host IP   | Host Port | Guest IP  | Guest Port |
| --------------------------- | -------- | --------- | --------- | --------- | ---------- |
| SSH                         | TCP      | 127.0.0.1 | 2222      | 10.0.2.15 | 22         |
| RabbitMQ management console | TCP      | 127.0.0.1 | 15672     | 10.0.2.15 | 15672      |
| Django Server               | TCP      | 127.0.0.1 | 8000      | 10.0.2.15 | 8000       |
| NTLM Scrutinizer            | TCP      | 127.0.0.1 | 5000      | 10.0.2.15 | 5000       |
| PostgreSQL                  | TCP      | 127.0.0.1 | 25432     | 10.0.2.15 | 5432       |

#### Config Window

[Download](https://www.python.org/downloads/release/python-3105/) and install Python 3.10.5

Create folder for DC Sonar project using [Git for Windows](https://git-scm.com/download/win) to it

```bash
cd '{PATH_TO_FOLDER}'
```

Make Windows installation steps for [dc-sonar-user-layer](https://github.com/ST1LLY/dc-sonar-user-layer#windows)

Make Windows installation steps for [dc-sonar-workers-layer](https://github.com/ST1LLY/dc-sonar-workers-layer#windows)

Make Windows installation steps for [ntlm-scrutinizer](https://github.com/ST1LLY/ntlm-scrutinizer#windows)

Make Windows installation steps for [dc-sonar-frontend](https://github.com/ST1LLY/dc-sonar-frontend#windows)

#### Set shared folders

Make [steps](https://gist.github.com/estorgio/0c76e29c0439e683caca694f338d4003#initial-steps) from "Open VirtualBox" to "Reboot VM", but add shared folders to VM VirtualBox with "Auto-mount" like below

![vm_shared_folders](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/vm_shared_folders.png)

After reboot run comand

```shell
sudo adduser $USER vboxsf
```

logout and login

In `/home/user` directory you are able to use mounted folders

```shell
ls -l
```

```shell
Output:
total 12
drwxrwx--- 1 root vboxsf 4096 Jul 19 13:53 dc-sonar-user-layer
drwxrwx--- 1 root vboxsf 4096 Jul 19 10:11 dc-sonar-workers-layer
drwxrwx--- 1 root vboxsf 4096 Jul 19 14:25 ntlm-scrutinizer
```

#### Config Ubuntu Server

##### Config PostgreSQL

[Install](https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart) PostgreSQL on Ubuntu 20.04

```shell
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql.service
```

Create DB admin

```shell
sudo -u postgres createuser --interactive
```

```shell
Output:
Enter name of role to add: admin
Shall the new role be a superuser? (y/n) y
```

Create dc_sonar_workers_layer DB acc

```shell
sudo -u postgres createuser --interactive
```

```shell
Output:
Enter name of role to add: dc_sonar_workers_layer
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) n
Shall the new role be allowed to create more new roles? (y/n) n
```

Create dc_sonar_user_layer DB acc

```shell
sudo -u postgres createuser --interactive
```

```shell
Output:
Enter name of role to add: dc_sonar_user_layer
Shall the new role be a superuser? (y/n) n
Shall the new role be allowed to create databases? (y/n) n
Shall the new role be allowed to create more new roles? (y/n) n
```

Create DB back_workers_db

```shell
sudo -u postgres createdb back_workers_db
```

Create DB web_app_db

```shell
sudo -u postgres createdb web_app_db
```

Run psql

```shell
sudo -u postgres psql
```

Set password for DB admin

```plsql
ALTER USER admin WITH PASSWORD '{YOUR_PASSWORD}';
```

Set password for dc_sonar_workers_layer

```plsql
ALTER USER dc_sonar_workers_layer WITH PASSWORD '{YOUR_PASSWORD}';
```

Set password for dc_sonar_user_layer

```plsql
ALTER USER dc_sonar_user_layer WITH PASSWORD '{YOUR_PASSWORD}';
```

Grant CRUD dc_sonar_workers_layer on back_workers_db

```plsql
\c back_workers_db
GRANT CONNECT ON DATABASE back_workers_db to dc_sonar_workers_layer;
GRANT USAGE ON SCHEMA public to dc_sonar_workers_layer;
GRANT ALL ON ALL TABLES IN SCHEMA public TO dc_sonar_workers_layer;
GRANT ALL ON ALL SEQUENCES IN SCHEMA public TO dc_sonar_workers_layer;
GRANT ALL ON ALL FUNCTIONS IN SCHEMA public TO dc_sonar_workers_layer;
```

Grant CRUD dc_sonar_user_layer on web_app_db

```plsql
\c back_workers_db
GRANT CONNECT ON DATABASE web_app_db to dc_sonar_user_layer;
GRANT USAGE ON SCHEMA public to dc_sonar_user_layer;
GRANT ALL ON ALL TABLES IN SCHEMA public TO dc_sonar_user_layer;
GRANT ALL ON ALL SEQUENCES IN SCHEMA public TO dc_sonar_user_layer;
GRANT ALL ON ALL FUNCTIONS IN SCHEMA public TO dc_sonar_user_layer;
```

Exit of psql

```
\q
```

Open pg_hba.conf

```shell
sudo nano /etc/postgresql/12/main/pg_hba.conf
```

Add line for connection from host machine to PostgreSQL, save and close

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
host    all             admin           0.0.0.0/0               md5
```

Open postgresql.conf

```
sudo nano /etc/postgresql/12/main/postgresql.conf
```

Change params to, save and close

```
listen_addresses = 'localhost,10.0.2.15'
shared_buffers = 512MB
work_mem = 5MB
maintenance_work_mem = 100MB
effective_cache_size = 1GB
```

Restart PG

```shell
sudo service postgresql restart
```

Check PG status

```shell
service postgresql status
```

Check log file if it's needed

```shell
tail -f /var/log/postgresql/postgresql-12-main.log
```

Now you can connect using admin acc and client such as [DBeaver](https://dbeaver.io/download/) from Windows

##### Config RabbitMQ

Install using [script](https://www.rabbitmq.com/install-debian.html#apt-quick-start-packagecloud)

Enable the management plugin

```shell
sudo rabbitmq-plugins enable rabbitmq_management
```

Create RabbitMQ admin

```bash
sudo rabbitmqctl add_user admin {YOUR_PASSWORD}
```

Tag the user with "rmq_admin" for full management UI and HTTP API access

```sh
sudo rabbitmqctl set_user_tags admin administrator
```

Open http://localhost:15672/

##### Install Python3.10

Ensure that your system is updated and the required packages installed

```shell
sudo apt update && sudo apt upgrade -y
```

Install the required dependency for adding custom PPAs

```shell
sudo apt install software-properties-common -y
```

Then proceed and add the deadsnakes PPA to the APT package manager sources list as below

```shell
sudo add-apt-repository ppa:deadsnakes/ppa
```

Download Python 3.10

```bash
sudo apt install python3.10=3.10.5-1+focal1
```

Install dependencies

```shell
sudo apt install python3.10-dev=3.10.5-1+focal1 libpq-dev=12.11-0ubuntu0.20.04.1 libsasl2-dev libldap2-dev libssl-dev
```

Install venv module

```shell
sudo apt-get install python3.10-venv
```

Check version

```shell
python3.10 --version

Output:
Python 3.10.5
```

##### Hosts

Add ip adresses of Domain Controlers to `/etc/hosts`

```
sudo nano /etc/hosts
```

#### Layers

##### Set venv

We have to create venv on level above as VM VirtualBox don't allow us to make it in shared folder

Go to home directory where shared folders located

```shell
cd /home/user
```

Make deploy [steps](https://github.com/ST1LLY/dc-sonar-user-layer#ubuntu) for dc-sonar-user-layer on Ubuntu

Make deploy [steps](https://github.com/ST1LLY/dc-sonar-workers-layer#ubuntu) for dc-sonar-workers-layer on Ubuntu

Make deploy [steps](https://github.com/ST1LLY/ntlm-scrutinizer#preparations-for-run) for ntlm-scrutinizer on Ubuntu

##### Config modules

Make config [steps](https://github.com/ST1LLY/dc-sonar-user-layer#config) for dc-sonar-user-layer on Ubuntu

Make config [steps](https://github.com/ST1LLY/dc-sonar-workers-layer#ubuntu) for dc-sonar-workers-layer on Ubuntu

Make config [steps](https://github.com/ST1LLY/ntlm-scrutinizer#preparations-for-run) for ntlm-scrutinizer on Ubuntu

##### Run

Make run [steps](https://github.com/ST1LLY/ntlm-scrutinizer#run) for ntlm-scrutinizer on Ubuntu

Make run [steps](https://github.com/ST1LLY/dc-sonar-user-layer#run) for dc-sonar-user-layer on Ubuntu

Make run [steps](https://github.com/ST1LLY/dc-sonar-workers-layer#run) for dc-sonar-workers-layer on Ubuntu

Make run [steps](https://github.com/ST1LLY/dc-sonar-frontend#run-development) for dc-sonar-frontend on Windows

Open https://localhost:8000/admin/ in browser on Windows host and agree with the self-signed certificate

Open https://localhost:4200/ in browser on Windows host and login as created Django user

### PyCharm settings

#### Interpreter

File -> Settings -> Python Interpreter -> Add ...

![add_interpreter](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/add_interpreter.png)



Choose "Existing environment" -> OK.

![choose_existing_env](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/choose_existing_env.png)

#### Docstring format

File -> Settings -> Python Integrated Tools -> Docstring format: Google -> Apply

![docstring_google](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/docstring_google.png)



#### Mypy

File -> Settings -> Other Settings -> Mypy

Path to Mypy executable: `{YOUR_PATH}\{PROJECT}\venv\Scripts\mypy.exe`

Path to config file: `{YOUR_PATH}\{PROJECT}\mypy.ini`

Apply

![mypy_settings](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/mypy_settings.png)



#### Pylint

Path to Pylint executable: `{YOUR_PATH}\{PROJECT}\venv\Scripts\pylint.exe`

Arguments: See specific setting in README.md of a repository.

Apply



#### Blue formatter

File -> Settings -> External Tools -> Add

![ext_tools_add](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/ext_tools_add.png)



Name: `Blue Formatter`

Description: `https://pypi.org/project/blue/`

Program: `$PyInterpreterDirectory$\blue.exe`

Arguments: `$FilePath$ --line-length=119`

Working directory: `$ProjectFileDir$`

![blue_formatter_tool](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/blue_formatter_tool.png)



OK -> Apply

Settings -> Keymap -> Blue Formatter -> Add Keyboard Shortcut

![add_shortcut_blue_formatter](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/add_shortcut_blue_formatter.png)



Enter `Alt+B` > OK -> Apply



#### Language Injections

Settings -> Editor -> Language injections

Disable python:"SQL select/delete/insert/update/create" -> Apply

![language_injections](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/language_injections.png)



#### Code Style

Hard wrap at: `119`

Visual guides: `79, 119`

Apply.

The limit of 79 chars for comments and docs

The limit of 119 chars for code

![code_style](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/code_style.png)



#### Useful shortcuts

Use `Alt + B ` for formatting

Use `Ctrl + Alt + O` for optimizing imports

### Commits style

https://www.conventionalcommits.org/en/v1.0.0/

https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#commit