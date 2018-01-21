### Issue stopping Apache

#### nginx

Another popular web-server that people are using, at least on Linux, is [nginx](http://nginx.org/en/).

Here is a nice [overview](https://www.digitalocean.com/community/tutorials/apache-vs-nginx-practical-considerations).

#### stopping Apache

First we check what ports are being used:

```
$ nc 127.0.0.1 80 < /dev/null; echo $?
0
```

The ``0`` means success in Unix, the port is being used.

Stop Apache and check again:

```
$ sudo service apache2 stop
$ nc 127.0.0.1 80 < /dev/null; echo $?
1
```
Port 80 is now free.

However, even though I stopped it I still get a response from Apache on local host with no port specified.  I get the default Apache page in the browser and with ``curl localhost``.

```
$ nc 127.0.0.1 80 < /dev/null; echo $?
0
$ nc 127.0.0.1 < /dev/null; echo $?
1
```

``ps aux | grep "apache"`` does not show anything.

Installation [notes](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-14-04-lts)

#### NAT

Note:  in previous tests we reassigned the port by using NAT, as we did in this project for ``ssh``.  However, that wasn't necessary for apache and we will try proceeding without it.

```
sudo apt-get install nginx
```

Our ip address is assigned by VirtualBox.  ``ifconfig`` has more data but it shows ``inet addr:10.0.2.15``.  I didn't need that for Apache, so again, we just note it and move on.

```
$ sudo service nginx restart
```

#### Start on boot

```
$ sudo update-rc.d nginx defaults
```

will make sure that nginx starts on boot.  To check if it's running do ``ps aux | grep "nginx"``.  It's already running on reboot so that's yet another thing we don't need to worry about.

#### Configuring nginx

```
sudo service restart nginx
```

``localhost`` still gives the Apache page both in Firefox and with curl.

At this point, I decide to wipe the VM and start over.