# DC Sonar Community

## Disclaimer

It's only for education purposes.

Avoid using it on the production Active Directory (AD) domain.

Neither contributor incur any responsibility for any using it.

## Content

1. [Description](#description)
   1. [Functionallity](#functionallity)
   2. [Architecture](#architecture)
2. [Suggestions and donations](#suggestions-and-donations)
3. [Style guide](#style-guide)
4. [Deployment for development](#deployment-for-development)
   1. [Windows host and Ubuntu Server guest](#windows-host-and-ubuntu-server-guest)
      1. [Set up the virtual machine](#set-up-the-virtual-machine)
      2. [Config Window](#config-window)
      3. [Set shared folders](#set-shared-folders)
      4. [Config Ubuntu Server](#config-ubuntu-server)
      5. [Layers](#layers)

## Description

### Functionallity

The DC Sonar Community provides functionality for analyzing AD domains for security risks related to accounts:

- Register analyzing AD domain in the app
- See the statuses of domain analyzing processes
- Dump and brute NTLM hashes from set AD domains to list accounts with weak and vulnerable passwords
- Analyze AD domain accounts to list ones with no expired passwords
- Analyze AD domain accounts by their NTLM password hashes to determine accounts and domains where passwords repeat

### Architecture

For the visual descriptions, open the [diagram files](https://github.com/ST1LLY/dc-sonar/tree/main/diagrams) using the [diagrams.net](https://www.diagrams.net/) tool.

The app consists of:

- The [dc-sonar-frontend](https://github.com/ST1LLY/dc-sonar-frontend) is the fronted part of the user web interface bases on:
  - [Angular](https://angular.io/)
  - [Angular Material](https://material.angular.io/)
- The [dc-sonar-user-layer](https://github.com/ST1LLY/dc-sonar-user-layer) is the backend part of the web app bases on:
  - [Python 3.10](https://www.python.org/downloads/)
  - [Django](https://www.djangoproject.com/)
  - [Django ORM](https://docs.djangoproject.com/en/4.0/topics/db/queries/)
  - [Django REST framework](https://www.django-rest-framework.org/)
  - [Celery](https://docs.celeryq.dev/en/latest/changelog.html)
  - [RabbitMQ](https://www.rabbitmq.com/)
  - [PostgreSQL](https://www.postgresql.org/)
- The [dc-sonar-workers-layer](https://github.com/ST1LLY/dc-sonar-workers-layer) is the logic layer that performs and runs analyzing processes which base on:
  - [Python 3.10](https://www.python.org/downloads/)
  - [SQLAlchemy](https://www.sqlalchemy.org/)
  - [Alembic](https://alembic.sqlalchemy.org/en/latest/tutorial.html)
  - [APScheduler](https://apscheduler.readthedocs.io/en/3.x/)
  - [RabbitMQ](https://www.rabbitmq.com/)
  - [PostgreSQL](https://www.postgresql.org/)
- The [ntlm-scrutinizer](https://github.com/ST1LLY/ntlm-scrutinizer) is the NTLM hashes performer with REST API based on:
  - [Python 3.10](https://www.python.org/downloads/)
  - [FastAPI](https://fastapi.tiangolo.com/)
  - [hashcat](https://github.com/hashcat/hashcat)
  - [impacket](https://github.com/SecureAuthCorp/impacket)



## Suggestions and donations

Feel free to suggest functionality and provide resources to implement it.

Bitcoin (BTC): bc1qlymngj29vvknxjl73u3w3u0s84qexpvgac4jhr

Ethereum (ETH): 0x4186c3d5b7B58C761Dd118AffCA1Cf24Ba8A3E89

Bitcoin Cash (BCH): qq5729vd50k3f57rwpnfh8kvhzn0lxurxvfsv4pmq8



## Style guide

See the information in [STYLE_GUIDE.md](https://github.com/ST1LLY/dc-sonar/blob/main/STYLE_GUIDE.md)



## Deployment for development

### Windows host and Ubuntu Server guest

In this case, we will set up the environment for editing code on the Windows host while running Python code on the Ubuntu guest.

#### Set up the virtual machine

[Create](https://www.virtualbox.org/manual/ch01.html#gui-createvm) a virtual machine with 2 CPU, 2048 MB RAM, 10GB SSD using [Ubuntu Server 22.04](https://ubuntu.com/download/server) iso in [VirtualBox](https://www.virtualbox.org/wiki/Downloads).

If Ubuntu installer asks for updating ubuntu installer before VM's installation - agree.

Choose to install OpenSSH Server.

VirtualBox Port Forwarding Rules:

| Name                        | Protocol | Host IP   | Host Port | Guest IP  | Guest Port |
| --------------------------- | -------- | --------- | --------- | --------- | ---------- |
| SSH                         | TCP      | 127.0.0.1 | 2222      | 10.0.2.15 | 22         |
| RabbitMQ management console | TCP      | 127.0.0.1 | 15672     | 10.0.2.15 | 15672      |
| Django Server               | TCP      | 127.0.0.1 | 8000      | 10.0.2.15 | 8000       |
| NTLM Scrutinizer            | TCP      | 127.0.0.1 | 5000      | 10.0.2.15 | 5000       |
| PostgreSQL                  | TCP      | 127.0.0.1 | 25432     | 10.0.2.15 | 5432       |

#### Config Window

[Download](https://www.python.org/downloads/release/python-3105/) and install Python 3.10.5.

Create a folder for the DC Sonar project.

Go to the project folder using [Git for Windows](https://git-scm.com/download/win):

```bash
cd '{PATH_TO_FOLDER}'
```

Make Windows installation steps for [dc-sonar-user-layer](https://github.com/ST1LLY/dc-sonar-user-layer#windows).

Make Windows installation steps for [dc-sonar-workers-layer](https://github.com/ST1LLY/dc-sonar-workers-layer#windows).

Make Windows installation steps for [ntlm-scrutinizer](https://github.com/ST1LLY/ntlm-scrutinizer#windows).

Make Windows installation steps for [dc-sonar-frontend](https://github.com/ST1LLY/dc-sonar-frontend#windows).

#### Set shared folders

Make [steps](https://gist.github.com/estorgio/0c76e29c0439e683caca694f338d4003#initial-steps) from "Open VirtualBox" to "Reboot VM", but add shared folders to VM VirtualBox with "Auto-mount", like in the picture below:

![vm_shared_folders](https://raw.githubusercontent.com/ST1LLY/dc-sonar/main/py_charms_settings_scrs/vm_shared_folders.png)

After reboot, run command:

```shell
sudo adduser $USER vboxsf
```

Perform logout and login for the using user account.

In `/home/user` directory, you can use mounted folders:

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

[Install](https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-20-04-quickstart) PostgreSQL on Ubuntu 20.04:

```shell
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql.service
```

Create the admin database account:

```shell
sudo -u postgres createuser --interactive
```

```shell
Output:
Enter name of role to add: admin
Shall the new role be a superuser? (y/n) y
```

Create the dc_sonar_workers_layer database account:

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

Create the dc_sonar_user_layer database account:

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

Create the back_workers_db database:

```shell
sudo -u postgres createdb back_workers_db
```

Create the web_app_db database:

```shell
sudo -u postgres createdb web_app_db
```

Run the psql:

```shell
sudo -u postgres psql
```

Set a password for the admin account:

```plsql
ALTER USER admin WITH PASSWORD '{YOUR_PASSWORD}';
```

Set a password for the dc_sonar_workers_layer account:

```plsql
ALTER USER dc_sonar_workers_layer WITH PASSWORD '{YOUR_PASSWORD}';
```

Set a password for the dc_sonar_user_layer account:

```plsql
ALTER USER dc_sonar_user_layer WITH PASSWORD '{YOUR_PASSWORD}';
```

Grant CRUD permissions for the dc_sonar_workers_layer account on the back_workers_db database:

```plsql
\c back_workers_db
GRANT CONNECT ON DATABASE back_workers_db to dc_sonar_workers_layer;
GRANT USAGE ON SCHEMA public to dc_sonar_workers_layer;
GRANT ALL ON ALL TABLES IN SCHEMA public TO dc_sonar_workers_layer;
GRANT ALL ON ALL SEQUENCES IN SCHEMA public TO dc_sonar_workers_layer;
GRANT ALL ON ALL FUNCTIONS IN SCHEMA public TO dc_sonar_workers_layer;
```

Grant CRUD permissions for the dc_sonar_user_layer account on the web_app_db database:

```plsql
\c back_workers_db
GRANT CONNECT ON DATABASE web_app_db to dc_sonar_user_layer;
GRANT USAGE ON SCHEMA public to dc_sonar_user_layer;
GRANT ALL ON ALL TABLES IN SCHEMA public TO dc_sonar_user_layer;
GRANT ALL ON ALL SEQUENCES IN SCHEMA public TO dc_sonar_user_layer;
GRANT ALL ON ALL FUNCTIONS IN SCHEMA public TO dc_sonar_user_layer;
```

Exit of the psql:

```
\q
```

Open the pg_hba.conf file:

```shell
sudo nano /etc/postgresql/12/main/pg_hba.conf
```

Add the line for the connection to allow the connection from the host machine to PostgreSQL, save changes and close the file:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
host    all             admin           0.0.0.0/0               md5
```

Open the postgresql.conf file:

```
sudo nano /etc/postgresql/12/main/postgresql.conf
```

Change specified below params, save changes and close the file:

```
listen_addresses = 'localhost,10.0.2.15'
shared_buffers = 512MB
work_mem = 5MB
maintenance_work_mem = 100MB
effective_cache_size = 1GB
```

Restart the PostgreSQL service:

```shell
sudo service postgresql restart
```

Check the PostgreSQL service status:

```shell
service postgresql status
```

Check the log file if it is needed:

```shell
tail -f /var/log/postgresql/postgresql-12-main.log
```

Now you can connect to created databases using admin account and client such as [DBeaver](https://dbeaver.io/download/) from Windows.

##### Config RabbitMQ

Install RabbitMQ using the [script](https://www.rabbitmq.com/install-debian.html#apt-quick-start-packagecloud).

Enable the management plugin:

```shell
sudo rabbitmq-plugins enable rabbitmq_management
```

Create the RabbitMQ admin account:

```bash
sudo rabbitmqctl add_user admin {YOUR_PASSWORD}
```

Tag the created user for full management UI and HTTP API access:

```sh
sudo rabbitmqctl set_user_tags admin administrator
```

Open management UI on http://localhost:15672/.

##### Install Python3.10

Ensure that your system is updated and the required packages installed:

```shell
sudo apt update && sudo apt upgrade -y
```

Install the required dependency for adding custom PPAs:

```shell
sudo apt install software-properties-common -y
```

Then proceed and add the deadsnakes PPA to the APT package manager sources list as below:

```shell
sudo add-apt-repository ppa:deadsnakes/ppa
```

Download Python 3.10:

```bash
sudo apt install python3.10=3.10.5-1+focal1
```

Install the dependencies:

```shell
sudo apt install python3.10-dev=3.10.5-1+focal1 libpq-dev=12.11-0ubuntu0.20.04.1 libsasl2-dev libldap2-dev libssl-dev
```

Install the venv module:

```shell
sudo apt-get install python3.10-venv
```

Check the version of installed python:

```shell
python3.10 --version

Output:
Python 3.10.5
```

##### Hosts

Add IP addresses of Domain Controllers to `/etc/hosts`

```
sudo nano /etc/hosts
```

#### Layers

##### Set venv

We have to create venv on a level above as VM VirtualBox doesn't allow us to make it in shared folders.

Go to the home directory where shared folders located:

```shell
cd /home/user
```

Make deploy [steps](https://github.com/ST1LLY/dc-sonar-user-layer#ubuntu) for dc-sonar-user-layer on Ubuntu.

Make deploy [steps](https://github.com/ST1LLY/dc-sonar-workers-layer#ubuntu) for dc-sonar-workers-layer on Ubuntu.

Make deploy [steps](https://github.com/ST1LLY/ntlm-scrutinizer#preparations-for-run) for ntlm-scrutinizer on Ubuntu.

##### Config modules

Make config [steps](https://github.com/ST1LLY/dc-sonar-user-layer#config) for dc-sonar-user-layer on Ubuntu.

Make config [steps](https://github.com/ST1LLY/dc-sonar-workers-layer#ubuntu) for dc-sonar-workers-layer on Ubuntu.

Make config [steps](https://github.com/ST1LLY/ntlm-scrutinizer#preparations-for-run) for ntlm-scrutinizer on Ubuntu.

##### Run

Make run [steps](https://github.com/ST1LLY/ntlm-scrutinizer#run) for ntlm-scrutinizer on Ubuntu.

Make run [steps](https://github.com/ST1LLY/dc-sonar-user-layer#run) for dc-sonar-user-layer on Ubuntu.

Make run [steps](https://github.com/ST1LLY/dc-sonar-workers-layer#run) for dc-sonar-workers-layer on Ubuntu.

Make run [steps](https://github.com/ST1LLY/dc-sonar-frontend#run-development) for dc-sonar-frontend on Windows.

Open https://localhost:8000/admin/ in a browser on the Windows host and agree with the self-signed certificate.

Open https://localhost:4200/ in the browser on the Windows host and login as created Django user.
