[link](https://www.cyberciti.biz/faq/ubuntu-linux-root-password-default-password/)

> The root user (also known as superuser), is a user on Ubuntu Linux and Unix-like systems with full administrative privileges (full access). So using root account for daily work can be very dangerous and you may damage your working system.

Normally, it is not recommended that you run as root.  However, if you ever did want to try this, you can open a new bash session like so:

```
te@te-VirtualBox:~$ sudo bash
[sudo] password for te: 
root@te-VirtualBox:~#
```

Or use ``sudo -s``.

Or use ``sudo -i``.

