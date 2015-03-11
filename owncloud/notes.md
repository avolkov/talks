Working with owncloud
============

# Overview

What is owncloud -- a client/server clone of popular cloud file hosting services such as Dropbox and Google drive. In essence its a client/server app with server being a bunch of PHP scripts that keep track and store your data, and client written in C++.


# Server


There is no hard dependencies on the variation of LAMP stack you use, so it's possible to have Nginx and Postgres  instead of Apache2 and MySQL setup outline in the most tutorials.

For the server, OwnCloud uses OpenSUSE build service so native package (a collection of PHP scripts) from third party (ownCloud) repository are available for CentOS, Debian, Fedora, openSUSE and Ubuntu.

Contacts, calendar,  collaborative document editing and bookmarks synchronization is supported

## Structure of the server

The PHP scripts are stored in /var/www/owncloud and sync data is located in /var/www/owncloud/data/<username>


As it turns out some things are hardcoded in the config and switching between IP addresses is problematic.

# Client

Client is in debian repository, and there's nothing special about setting it up.

Much like other file sync services web and smartphone clients are available (Android - $0.99)



# Sync


Configuration is stored in ${HOME}/.local/share/data/ownCloud/

Cloud client tries to sync over the network as soon as it sees the server.


# Configuration


## Client configuration

### owncloud.cfg
Generally describes connection, authentication and some local window size opsition.
Everything is in plain text


    [General]
    optionalDesktopNotifications=true

    [ownCloud]
    url=http://172.16.200.102/owncloud
    http_user=admin
    authType=http
    user=admin

### folders directory

By default there's one file in this directory ownCloud, that specifies root mapping of a directory on the server to the root mapping of a directory on the client.

Nothing really stops you from creating multiple subdirectories and mapping them to arbitrary places. Once file is saved an ownCloud client is restarted synchronization begins.

    [photos]
    localPath=/home/alex/photos/
    targetPath=/photos
    backend=owncloud
    connection=ownCloud

### Ignore file

Configuring ignore files is done through [Ignore File Editor](https://doc.owncloud.org/desktop/1.4/visualtour.html#ignoredfileseditor-label)


# Server configuration


## Nginx and Postgres

[OwnCloud Nginx configuration](https://doc.owncloud.org/server/7.0/admin_manual/installation/nginx_configuration.html)

[Postgres configuration](https://doc.owncloud.org/server/7.0/admin_manual/configuration/database_configuration.html#postgresql-database)


## More customizations

Good news! It's all open source PHP scripts so only your imagination prevents you from doing stupid things, like running [OwnCloud on OpenWRT](http://wiki.openwrt.org/doc/howto/owncloud), on something like TL-WDR3500

## External Storage

Configured through GUI, presets for Amazon S3, Dropbox, Google Drive, OpenStack, FTP/SFTP etc. is available
https://doc.owncloud.org/server/7.0/admin_manual/configuration/custom_mount_config_gui.html

## Encryption
Transport layer encryption -- you have to set up your own SSL certificates. Most likely self-signed SSL certificates, it is more of a server-configuration issue rather than anything ownCloud specific

Encrypted data storage -- is implemented with 'Encryption App' mostly intended for storing data on other file-sharing services. Encryption and decryption keys are stored on the server, which means you have to trust server administrator, and the manual recommends using whole-disk encryption on a partition where ownCloud files are stored. Encryption uses client's password and AES-256-CFB algorithm.

Basically it's a PHP wrapper around a few OpenSSL calls, so the files can be decrypted with the command like --

openssl aes-256-cfb -d -in <encrypted file> -out <decrypted file> -iv <salt?> -k <recovery key>

There's also exists [PHP wrapper](http://blog.schiessle.org/2013/05/28/introduction-to-the-new-owncloud-encryption-app/comment-page-1/#comment-61180) for the function above.

# Resource usage.

Standard Apache/Mysql configuration with no customizations, as tested on a KVM virtual machine on a local network:

At 1GB RAM and 1 CPU -- sync choked at 3 MB/s

At 2GB RAM and 2 CPUS -- sync maxed out at 7.5MB/s with light server-side resource use.

# My experience using own cloud

Setting up ownCloud as a home directory is a major pain as you have to specify all files that should be ignored, and there are lots that already have different methods of synchronization: git repositories, firefox sync, folders that are already being managed with rsync.
s
Much better approach is to add folders one-by-one starting with most stable and least changed.
