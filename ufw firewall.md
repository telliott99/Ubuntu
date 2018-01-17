#### ufw firewall

[ufw setup](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04)

[cheat-sheet](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)

Examples:

- ``$ sudo ufw status verbose``
- ``$ sudo ufw enable``
- ``$ sudo ufw allow ssh``
- ``$ sudo ufw allow 443``
- ``$ sudo ufw allow https``
- ``$ sudo ufw deny from 15.15.15.51``
- ``$ sudo ufw allow from 15.15.15.51 to any port 22``

```
te@te-VirtualBox:~$ sudo ufw app list
[sudo] password for te: 
Available applications:
  CUPS
  OpenSSH
te@te-VirtualBox:~$ sudo ufw allow OpenSSH
Rules updated
Rules updated (v6)
te@te-VirtualBox:~$ sudo ufw enable
Firewall is active and enabled on system startup
te@te-VirtualBox:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             

te@te-VirtualBox:~$
```

Check from macOS that we can still login:

```
> ssh -p 2222 te@127.0.0.1
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.10.0-28-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

262 packages can be updated.
133 updates are security updates.

Last login: Tue Jan 16 14:00:21 2018 from 10.0.2.2
te@te-VirtualBox:~$ logout
Connection to 127.0.0.1 closed.
>
```