#### ssh using a public/private key pair

We first test password authentication.  We did this last time but let's check the server key.  The SHA-256 digest of the key we generated on the server was:

```
8PEWkTXlIzbYhsmL81npx3nUOS16LFjWbH6bDWMOH2M
```

We can recheck that on Ubuntu:

```
$ sudo ssh-keygen -l -f /etc/ssh/ssh_host_ecdsa_key
256 SHA256:8PEWkTXlIzbYhsmL81npx3nUOS16LFjWbH6bDWMOH2M root@te-VirtualBox (ECDSA)
```

and also compare with the key we accepted into ``known_hosts`` on macOS ---    [instructions](http://superuser.com/questions/529132/how-do-i-extract-fingerprints-from-ssh-known-hosts):

```
> ssh-keygen -l -f ~/.ssh/known_hosts
..
256 SHA256:8PEWkTXlIzbYhsmL81npx3nUOS16LFjWbH6bDWMOH2M [127.0.0.1]:2222 (ECDSA)
>
```

The login works (not shown).

#### Edit config

Use vi on Ubuntu

```
$ cd /etc/ssh
$ vi sshd_config
..
PubkeyAuthentication yes
```

This is already set.  We leave PasswordAuthentication ``yes`` because we still need to transfer the host's public key to the server.

#### Use scp for key transfer

On macOS Terminal (but with my Ubuntu password):

```
> scp -P 2222 ~/.ssh/ubuntu_id_rsa.pub te@127.0.0.1:~/.ssh/authorized_keys
Password: 
ubuntu_id_rsa.pub                               100%  419   600.9KB/s   00:00    
>
```

On the Ubuntu guest, check the digest of the key in the file we just copied:

```
te@te-VirtualBox:/etc/ssh$ ssh-keygen -l -f ~/.ssh/authorized_keys
2048 SHA256:roDxC6dAH8uvVtHQq64+fZYYOrms1Ym34Eygb1ns12w telliott_admin@Toms-MacBook-Air.local (RSA)
```

Now, check that against the source.  On the host:

```
> ssh-keygen -l -f ~/.ssh/ubuntu_id_rsa.pub
2048 SHA256:roDxC6dAH8uvVtHQq64+fZYYOrms1Ym34Eygb1ns12w telliott_admin@Toms-MacBook-Air.local (RSA)
> 
```

#### Edit ``ssh_configd`` and restart

We need to edit ``ssh_configd`` on Ubuntu so that this line reads

```
AuthorizedKeysFile	%h/.ssh/authorized_keys
```

Now restart the service.

```
sudo service ssh restart
```

Login works:

```
> ssh -p 2222 te@127.0.0.1
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.10.0-28-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

262 packages can be updated.
133 updates are security updates.

Last login: Tue Jan 16 12:46:48 2018 from 10.0.2.2
te@te-VirtualBox:~$ ls
Desktop    Downloads         Music     Public     Videos
Documents  examples.desktop  Pictures  Templates
te@te-VirtualBox:~$ 
```

Last but not least:  edit with vi and change to

```
PasswordAuthentication no
```

Retest.  On Ubuntu:

```
sudo service ssh restart
```

On the host:

```
> ssh -p 2222 te@127.0.0.1
Welcome to Ubuntu 16.04.
..
```

Looking good.