# Linux Server Configuration

#### Details

- **IP Adress:** 54.68.7.26
- **SSH port:** 2200
- **Address:** http://54.68.7.26/events
- **User `grader` password:** UdaLinuxProject2016


Unless otherwise indicated, all the Linux shell instructions are done logged as `root`

#### Launch your Virtual Machine with your Udacity account.
Content of **grader.rsa**:
```
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAyWTkj+NJNwrolVhskbxxLv+j7bopMPgdXj0FLdWiq6dWE4IO
I0CIjc5b/D5yI0D5JpCJfCR0YFLiAayCjS/wL8/Yw0K/FXdfoi01FiQtE+tDmC64
WcynqCLJy9dX+Yaq2AWfiEY/AoAUWur8S6lOOFlWTCytPhnGsDEm8TbCs9/Lf/gx
m6ylnnaIsoQu58p5GXjUcRW8ezK4mlk/hh9HdQne8SrkXlR6gwdD3e5BmWyJDKuB
CLJb3h+pP/GW/2Zx+TG93gnrYMztPKC07MQHLe2f+MHuNdfBzQIy3ymFi7UUjlIe
+FI5EJMxyyR9aVMucifjwlVIkDyZC5EtVJ94tQIDAQABAoIBAFqalWlWa53jvndR
7KY0DQeD4xAUbGundC7tbOMN9b0b71GfdRTO96A0cpWs0lSh6nU7GV9MVz7uitcN
8T3rjzczXuWZpbXWmqOviu6HH/CSKzrP6ggcVmECJZ8bJm0P9NRy0y7Py5EoZf/x
L8gqRxJ6vqROhTInDog91LM4W6fn/inRYqpGCgC1uPGv0H3J2aZFptwfRc5k55Ah
RfKxBKuMS9MiTNcTvv3qsDAe6TiG+QCMUZSt6THuLntIvloT7YPsoMKxOJyPJmKZ
f0AbqM+jHkDhKD2TW36qczeKvvEw5WSTwJpk6yvBUeD7G2ETaiBviTY6xAhUWX8M
YixlqhECgYEA85wo7JaKvkQxE9fIC54huGWNNX56Zh8fbz8Q1NY5xMNlBaRIdXpH
eFWVsrRtRbT8gnBlLq0eSeOcH3v0bhKAKO+xw0rglnl+Sre6afjzmrewU57RBRyo
qGfvK1N5yLoKp3J5x1l+piqMFBkvCZWaZSKpsxuFeX17ZDUiTWIlXPcCgYEA06MT
VmxPrSei5wT1+vOTgJ+ky4+s3A7zG+KWIEJvMEXmolx+r9NFUuCR6vOKR7behpAA
14GusUFEAxoX0Z9lQ6e24dFY0IeFn2m02tzMEwlTF7v9roTIftNctjBlq47zD3ZG
HaLgcgCyW+C7hL1C87fPPLj54gJkqv8n6Mv2SLMCgYAxpHHmfGr7iNPjckZur6O5
euBCtWpl4xO5zMU2Sg1tnk+4zZiCcA+eYvpld0UJPm9yeUr1LwwMDJjlBZJy18xM
C0RTQq/vmgR1X2labQPfVG1XLPb1Lrjj0II5dwJLODYxly9fw0n+vI7dDaC7OERF
NcIVIJebgUmiyv0ZfdWVUwKBgHLWFQMbCGzTihBC39pI2uu7NGeEUW6i6gVigMWG
ZuDRvSCygceUIxSiaGsNtSXOs5ZegyZs5salvGrWeMA09nSsh46m0sNsFfR3Hjx3
/IAD2kORrUhKIqS2uaLeRfy+N2NXcRpZq+IQQLecDQQs0ix+u4Oed3DMjaU/iH9I
VR4DAoGBAI+oTyNSdD6stupfnRroNC5CMb7+XMXCW0T0bC8f/5700CUM8/z+s9Gk
rsi6GSECT4/X2iL0GImtIl3gi33auqZ9kMTEYv7GlXek3Fe9AE74AF7b/Ml9vfo6
soW1FZHo6Bt6P0Jr+C1MduD4jC4ojmwqi2L8oPubpmlC7Iu20TXf
-----END RSA PRIVATE KEY-----
```

#### Follow the instructions provided to SSH into your server
`ssh -i ~/.ssh/udacity_key.rsa root@54.68.7.26`

#### Create a new user named `grader`
`adduser grader`

Locally, I created a key pair using `ssh-keygen`, copied the content of the public key and, logged in the Linux server as `root`, I created the file `/home/grader/.ssh/authorized_keys` and pasted the content.

#### Give the `grader` the permission to sudo
I created a file named `grader` in `/etc/sudoers.d` with the content:

`grader ALL=(ALL) NOPASSWD:ALL`

#### Update all currently installed packages
```
$ apt-get update
$ apt-get upgrade
$ apt-get autoremove
```

#### Change the SSH port from 22 to 2200
Using `nano` editor, I changed `Port 22` by `Port 2200` in the file `/etc/ssh/sshd_config` and then `ssh restart`.

#### Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
```
$ ufw default deny incoming
$ ufw default allow outgoing
$ ufw allow ssh
$ ufw allow http
$ ufw allow ntp
$ ufw enable
```

#### Configure the local timezone to UTC
`hwclock --utc --systohc`

#### Install and configure Apache to serve a Python mod_wsgi application
`$ apt-get install apache2`
`$ apt-get install libapache2-mod-wsgi`

#### Install and configure PostgreSQL:
`apt-get install postgresql`

#### Do not allow remote connections
Allow remote connections is disabled by default when installing **PostgreSQL** from the Ubuntu repositories, but I double checked with
````
cat /etc/postgresql/9.5/main/pg_hba.conf
````
This is the important part of the configuration file:
```
# Database administrative login by Unix domain socket
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
```

#### Create a new user named `catalog` that has limited permissions to your catalog application database
Once PostgreSQL installed, I changed to the default user `su - postgres`, then created the `catalog` user with `createuser --interactive`.

Then created the `events` database with `createdb events`. After that, I got into PostgreSQL shell with `psql` and denied acces to all users except `postgres` to all the existing databases, **postgres**, **template0** and **template1** using
```
REVOKE ALL ON DATABASE <database> FROM PUBLIC;
```
This way, as by default databases are created with public access, `catalog` user will be able to access **only** to `events` database.

#### Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!

I installed Git using `apt-get install git`, went to directory `/var/www` and then cloned my Git respository using
```
git clone https://github.com/manelromero/catalog.git --branch linux
```

I then changed the `/etc/apache2/sites-enabled/000-default.conf` file content to
```
<VirtualHost *:80>
	ServerName 54.68.7.26
	
	WSGIScriptAlias / /var/www/catalog/events.wsgi

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

#### Changes made after first project submission
I changed to `PermitRootLogin no` in the `/etc/ssh/sshd_config` file and then restarted the ssh server with `service ssh restart` in order to avoid `root` login.

Logged as `grader`, I deleted the firewall rules that were allowing ssh connection in port 22 finding the rule number with `sudo ufw status numbered` and deleting the rules with `sudo ufw delete 2`

I add the `grader` user password to the beginning of this file and changed the RSA private key for the one of the `grader` user as `root` remote connections are not allowed anymore.