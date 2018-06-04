# Installation

## Automatic Installation

When using the automatic installer, the server must be Ubuntu Server 18.04.

Please read through the installation moments. There is a one-line command in the end if that's what you prefer.

### Login as root

If you are not logged in as root, switch to root.

```bash
sudo su
```

### Updating, upgrading and installing git

It's always best to do a fresh upgrade to be sure you have the latest versions of software available.

Git is used to download the Libki server.

```bash
apt update && apt upgrade -y && apt install git -y
```

### Downloading and running the installer

First download the Libki server.

```bash
git clone https://github.com/libki/libki-server.git
```

Enter the downloaded directory.

```bash
cd libki-server
```

Run the installer.

```bash
./install.sh
```

### One-line installer

This is the whole installation process in one single line.

```bash
apt update && apt upgrade -y && apt install git && git clone https://github.com/libki/libki-server.git && cd libki-server && ./install.sh
```

## Manual installation

If you wish to completely build the Libki server by yourself, these guidelines should help you along the way. This is written towards someone new to terminal and a lack of GUI.

First of all, update and upgrade.

```bash
apt update && apt upgrade -y
```

### Dependencies

```bash
apt install cpanm curl perl git make build-essential unzip mysql-server ntp -y
```

You might need these packages too, depending on your server. If you encounter error messages when installing the needed perl modules, this is the place to start.

```bash
apt install libmysqlclient-dev libxml-parser-perl libxml-libxml-perl
```

### Setting up a user

It is suggested to setup a new user account to run the server from. From here on, we'll use the username **libki**. Give it a good, strong (and preferrably memorable) password.

```bash
adduser libki
```

### Clone the repository

Clone the repo to the home directory of your newly created user.

```bash
git clone https://github.com/libki/libki-server.git /home/libki/libki-server
```

### Install needed perl modules

```bash
cd /home/libki/libki-server
cpanm -n --installdeps .
```

This will take a while. Make sure it ends with saying everything was installed correctly. If not, you will need to fix what's missing.

Now we need to add the Libki server's perl module to our $PATH, so our shell knows where to find it.

```bash
echo "export PERL5LIB=$PERL5LIB:/home/libki/libki-server/lib" >> ~/.bashrc
```

We also need to add it to the libki user's $PATH.

```bash
echo "export PERL5LIB=$PERL5LIB:/home/libki/libki-server/lib" >> /home/libki/.bashrc
```

### Create a database

Depending on your MySQL or MariaDB version, it may or may not want you to log in with a username and password. If you are to log in with a username and password, the username is **root** and you chose the password during the dependencies installation. 

If that's the case, start MySQL with ```mysql -uroot -p```.

If you didn't get to create a root password during installation, just start MySQL with ```mysql```.

Once inside MySQL, we need to create a database and a user. Note that all aphostrophes are essential and cannot be omitted.

```sql
CREATE DATABASE libki;
CREATE USER 'libki'@'localhost' IDENTIFIED BY 'CHOOSEAPASSWORD';
GRANT ALL PRIVILEGES ON libki.* TO 'libki'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Populate the database and create an admin account

We got our perl modules, we have an empty database... Let's fill it with things.

```bash
./installer/update_db.pl
```

In order for Libki to access the database, we must give it the login information. Make a copy of the libki_local.conf.example file and remove .example. Open it and change the password to the one you chose.

```bash
cp libki_local.conf.example libki_local.conf
nano libki_local.conf
```

You save and close with Ctrl-O and Ctrl-X, respectively.

Now create an admin account, so we can access the administration pages once the server is up and running.

```bash
./script/administration/create_user.pl -u ADMINUSERNAME -p ADMINPASSWORD -s
```

### Setting up log files

The Libki server will produce log files. Their default location is /var/log/libki. Even if you don't want to change the default location, you still need to make a working copy the log4perl.conf.example file.

```bash
cp log4perl.conf.example log4perl.conf
```

### Installing cron jobs

Part of what makes the Libki server tick is scheduled jobs called cron jobs. ```./script/cronjobs/libki.pl``` is the timer and ```./script/cronjobs/libki_nightly.pl``` is the cleaner that resets everything overnight.

There are two pre-written cron files, just to import. The first one is for the libki user and the second one for root.

```bash
cat installer/cron/libkicron | crontab -u libki -
cat installer/cron/rootcron | crontab -
```

### Create a Libki service

Copy the init template to /etc/init.d.

```bash
cp init-script-template /etc/init.d/libki
```

If you want to edit the port of the server (if you, for example, want to run it on port 80 and don't want to use a reverse proxy), this is the time. Open it up, change port number from 3000 to 80 (or something else), save and close.

Finally, run update-rc.d to enable Libki as a service.

```bash
update-rc.d libki defaults
```

### Start the server

```bash
service libki start
```

If all went well, you should have a server up and running by now. You can visit it on http://127.0.0.1:3000/administration.

### Manual install optional: Set up your reverse proxy

Make sure you're logged in as root. 

* Install Apache

```bash
apt-get install apache2
```

* Navigate to the libki-server directory

```bash
cd /home/libki/libki-server
```

* Run the apache_setup.sh script

This disables the old default conf, copies reverse_proxy.config to Apache's folder and enables both the Libki reverse proxy and the needed modules..

```bash
./script/setup/apache_setup.sh
```

* Restart apache

```bash
service apache2 restart
```
## OPTIONAL: Configuring Libki to authenticate against a SIP server

To enable SIP authentication, you will need to edit your libki\_local.conf and add a section like this:

```text
<SIP>
    enable 1
    host ils.mylibrary.org
    port 6001
    location LIB
    username libki_sipuser
    password PassW0rd
    terminator CR
    require_sip_auth 0
    enable_split_messages 0
    fee_limit 5.00 # Can be either a fee amount, or a SIP2 field that defines the fee limit ( e.g. CC ), delete for no fee limit
    deny_on charge_privileges_denied    # You can set SIP2 patron status flags which will deny patrons the ability to log in
    deny_on recall_privileges_denied    # You can set as many or as few as you want. Delete these if you don't want to deny patrons.
    deny_on excessive_outstanding_fines # The full listing is defined in the SIP2 protocol specification
    deny_on_field AB:This is the reason we are daying you  # You can require arbitrary SIP fields to have a value of Y for patrons to be allowed to log in.
                                                           # The format of the setting is Field:Message
</SIP>
```

The SIP section requires the following parameters:

* enable: Set to 1 to enable SIP auth, 0 to disable it.
* host: The SIP server's IP or FQDN.
* port: The port that SIP server listens on.
* location: The SIP location code that matches the sip login.
* username: The username for the SIP account to use for transactions.
* password: The password for the SIP accouant to use for transactions.
* terminator: This is either CR or CRLF depending on the SIP server. Default is CR
* require\_sip\_auth: Does this SIP server require a message 93 login before it can be used? If so this should be set to 1 and the username/password fields should be populated. This should be set to 1 for Koha.
* enable\_split\_message: IF thie server supports split messages you can enable this. This should be set to 0 for Koha.
* fee\_limit: As notated, this can be a set number or a SIP field to check. If the fee limit is exceeded, the user login will be denied.
* deny\_on: This can be repeated to deny logins based on the patron information flags detailed in the SIP2 protocol specification.
* deny\_on\_field: This can be repeated to deny logins if the Specified field does not have a value of "Y".

### Troubleshooting

You can now test to see if your server is running by using the cli web browser 'links'. If you don't have links installed you can installed it via the command

```bash
sudo apt-get install links
```

Now, open the Libki public page via:

```bash
links 127.0.0.1:80
```

If this loads the Libki login page, congrats! If you get an error, you can try bypassing the proxy and access the server directly on port 3000.

```bash
links 127.0.0.1:3000
```

If this works, then you'll want to check your Apache error logs for the failure reason. If it does not work, you'll want to check the Libki server error log instead. It can be found at /home/libki/libki\_server.log if you've followed this tutorial closely.

