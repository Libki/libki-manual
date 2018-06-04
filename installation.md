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

