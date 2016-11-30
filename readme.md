# Custom Deployment

## Learning Objectives

- Configure a linux web server
- Set up public key ssh authentication
- Deploy a nodejs app
- Configure a reverse proxy with nginx

## Set up droplet

Please use my referral code - well both get 10 bucks in credits!

<http://jshawl.com/do>

All the defaults are fine. Choose the cheapest droplet.

All the way at the bottom, add your ssh key.

You can get your ssh key by typing in

```
$ cat ~/.ssh/id_rsa.pub
```

This will allow you to log into the server without having to type your password.

```
$ ssh root@i.p.address.here
```

## Create a user and password

```
$ useradd -m theusernameyouwant -G sudo -s /bin/bash
$ passwd theusernameyouchose
```

## `/etc/hosts`

Remembering an IP address is difficult and time consuming. Let's fake a domain name
by editing our hosts file. This file is consulted before asking DNS servers what
ip address a domain points to.

```
sudo nano /etc/hosts
```

add the following line at the bottom:

```
your.ip.address.here example.com
```

Feel free to replace example.com with whatever domain you'd like.

Test it out!

```
$ ssh yourusername@example.com
$ mkdir .ssh/
```

## Pub key auth

Typing in a password is difficult and time consuming. We can prevent having to type
in a password by configuring public key authentication.

Locally...

```
$ cat ~/.ssh/id_rsa.pub | ssh yourusername@example.com "cat >> ~/.ssh/authorized_keys"
$ ssh yourusername@example.com
```

You'll know you did this correctly if you are logged in, but not prompted for a password.

## Install git

```
$ sudo apt-get install git
$ mkdir ~/git
$ git init --bare ~/git/yourdomain.com
```

### Try it out!

cd to your local copy of your site

```
$ git remote add production yourusername@yourdomain.com:git/yourdomain.com
$ git push production master
```

## Install nginx

Now it's time to serve up that site!

```
sudo apt-get install nginx
```

visit yourdomain.com in a browser - holy smokes!

Instead, it would be cooler if nginx showed our website.

### Two steps:

1. clone our site to some folder
2. tell nginx to use that folder as the document root

On the server...

```
$ sudo chown -R yourusername:yourusername /var/www
$ git clone ~/git/yourdomain.com /var/www/yourdomain.com
$ sudo nano /etc/nginx/sites-enabled/yourdomain.com
```

add the following lines:

```
server{
  listen 80;
  server_name yourdomain.com;
  root /var/www/yourdoman.com;
}
```

reload nginx

```
$ sudo service nginx reload
```

## post update hook

You can currently deploy your site by pushing to your git repo, and pulling into
the website's directory, but git can do this automatically for you.

```
$ cd ~/git/yourdomain.com/hooks
$ mv post-update.example post-update
$ chmod +x post-update
```

replace that file with:

```
#!/bin/bash -l

cd /var/www/yourdomain.com
unset GIT_DIR
git pull origin master
```

Try it out! make a change to your site locally and push

## Install node

https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions

## Configure reverse proxy

## Forever and Crontab
