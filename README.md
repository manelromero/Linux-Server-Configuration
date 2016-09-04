# Linux Server Configuration

#### Details

- **IP Adress:** 54.68.7.26
- **SSH port:** 2200
- **Address:** http://54.68.7.26/events


Unless otherwise indicated, all the Linux shell instructions are done logged as `root`

#### Launch your Virtual Machine with your Udacity account.
Content of **udacity_key.rsa**:
```
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEArEVaf5ULAlsQAPR9l7eNV5lrPVZwJdOYyRYOAQV3hPbb1Rg/
NmS32G3Pd8ec7m3fObH5oI1CeC4XfkMUQUG7kc1MIzXdvA07hRjLd6ollF+mSTp7
ttw1o+yINrrWXpMGzn5awXgpTPXa5dFexa04N7qUY56sdpEAjzktdGYRCG4glcxC
NI1nAERak4YcIZ0//gBv5cCzKSuLHAc5gu4H5bVw3yOlSvf6KIYUwAsOmclXW3yV
zA8NTCfmN+WjJInIaBWZ1ArbTTSW0xVDC0o5R9XmNfYswH1kqRlWB5Ha9So1N9FR
I7NyfaR1tvQl1ygOS9Fv/rxoHgGltbd+VuhGgQIDAQABAoIBACVEB3Sr6CrFSiv6
t6R3R3MYtBMbvLziDh4J0FO2PAmeYSWyzhHVLdP6xTOTEcaeK/jOIELz69oVlm1w
01PehVXgBEVlpKbGcgazu2gSfXigTFdw+EwxTMU+KKU2O6NPyNwohzds5/pHAOhQ
J2E5vHyJtwnpzEMF0Qoqg0O3xJ1+4mAiSE69ApcXwVKWJYjENk36ltWV7meaHY7Y
ZJojFHASO0UrPpxrlpfXw6Zuiu/2BPf+CG5WMnbEHZEIZOl4myxfwilvYU758G/9
lD6JMyvXkHlUh19sRsZf+ZmZ4s8xfIBVtJamEAzzjCJDz05n/g9vsxFPxAp4HRCB
ceI5qTECgYEA0jwQmImQOG9uPGLYBhrrIqtUXVqjgRe/Eskok1qw7+6IFkVRWi6C
6TXXFQYtGLqEcebMq2mSUSN/tVFt0vkX9bZIm5h7WTdQ/n2t0uCp8sUINJNNXXCN
DyCYE/o4UDdKp+HtghbAa1U0V9hTaBLz36nFVb6wtfmAhUORHh0I6g0CgYEA0cWm
fdL6/bNWf6htp63JHlAJpjtb0aG/S+c2WIlt7bVdJU5i5rMtz7eAw1an9kvioQ3Z
o3wQMgnN8Wz/Ga8S5s+UI3/qikuRu7o344Pk2EBpQLlpZcR82nXNTFo/Xo3DUNit
D2H87cl7M9ATG0zI5QXx+3+J3+6VCNDMNUqetUUCgYB3zSaf5fQ7VLR60NDNWdWY
Yg2nTRlTXRbTO7JVlh6x+zmthz6+pzaOctHn9ycHqnIC/kSvgW+RsSr7RjXRPYU7
ze4+THwt8OnEvBWrz77IyyLL4wz5LEQV1LsU10fz3i460PIpaZGpGFrFnE6bRgGW
+QWswQeuC0oDpucxQ9ZqmQKBgQC36nWNbZP3KOwWoqH5WGmzdPLw1qwrAIgK0V2Z
hGxn9BIFalNV9xScQEPZf7llPKUnFVCSgGxmoqf4GtsSNqf4y1gTQ8xrkdPk5mCn
zfSvENH3GFhrDDl3A7+KSLm0edAbqs0YZo3uIS8ufyixT8H24h6FpQ1J0xDBmObh
dlntjQKBgBg6avDqHWWx8O9SGat3oDFXLUPtlMLww9OVWfuz4g+cBqkNJtz5MRWk
qAd9w4srAk2UYZ8lPnAVUjZxoCreIAL6w/6ktGRmzYC0oLPLE0QhmPrDvNI1zoK7
63X5EArtUxU4HJOGM6ZmpALYt9bvdUEa95SRfDHH6uu8PbsdG5rN
-----END RSA PRIVATE KEY-----
```

#### Follow the instructions provided to SSH into your server
`ssh -i ~/.ssh/udacity_key.rsa root@54.68.7.26`

#### Create a new user named `grader`
`adduser grader`.

Locally I created a key pair using `ssh-keygen`, copied the content of the public key and, logged as `root`, I created the file `/home/grader/.ssh/authorized_keys` and pasted the content.

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
`apt-get install apache2`

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

Then created the `events` database with `createdb events`. After that, I got into PostgreSQL shell with `psql` and deny acces to all users except `postgres` to all the existing databases, **postgres**, **template0** and **template1** using
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
	ServerName example.com
	
	WSGIScriptAlias / /var/www/catalog/events.wsgi

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
