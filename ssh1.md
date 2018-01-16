#### Linux:  SSH, part 1

Our goal here is to login to the guest Ubuntu machine (on VirtualBox) from the command line in Terminal on the host macOS.

In order to do that we need four things:

- a server on Ubuntu, ``openssh-server``
- establish our credentials for the logon
- tell VirtualBox how to do NAT as we need
- discover the command to issue in OS X

#### SSH server

Let's start with the server.  Grab the openssh server software [here](https://help.ubuntu.com/lts/serverguide/openssh-server.html)

```
sudo apt-get install openssh-server
```

While the install runs I see this output:

```
    Creating SSH2 RSA key; this may take some time ...
    Creating SSH2 DSA key; this may take some time ...
    Creating SSH2 ECDSA key; this may take some time ...
    Creating SSH2 ED25519 key; this may take some time
```

We'll be using one of these keys eventually (``ssh_host_ecdsa_key``).

I'd like to check the key fingerprints.  Where are the key files?  

They are in this directory ``/etc/ssh``.

```
te@te-VirtualBox:/etc/ssh$ ls
moduli            ssh_host_dsa_key.pub    ssh_host_ed25519_key.pub
ssh_config        ssh_host_ecdsa_key      ssh_host_rsa_key
sshd_config       ssh_host_ecdsa_key.pub  ssh_host_rsa_key.pub
ssh_host_dsa_key  ssh_host_ed25519_key    ssh_import_id
```

As it turns out, the key we'll use is the ``ecdsa`` key, and its fingerprint is:

```    
te@te-VirtualBox:/etc/ssh$ sudo ssh-keygen -l -f /etc/ssh/ssh_host_ecdsa_key
256 SHA256:8PEWkTXlIzbYhsmL81npx3nUOS16LFjWbH6bDWMOH2M root@te-VirtualBox (ECDSA)
```

That's 

```
8PEWkTXlIzbYhsmL81npx3nUOS16LFjWbH6bDWMOH2M
```

#### Test login from Ubuntu

We'll set up the server in a minute, but the next part of the puzzle is to figure out how to connect.  We need an IP address and a port.  To test it, we can find out about this in Ubuntu:

```
te@te-VirtualBox:/etc/ssh$ ifconfig
enp0s3    Link encap:Ethernet  HWaddr 08:00:27:9c:22:f8  
          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
          inet6 addr: fe80::bcd9:c5d3:68c5:7489/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:36385 errors:0 dropped:0 overruns:0 frame:0
          TX packets:7192 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:43996378 (43.9 MB)  TX bytes:439961 (439.9 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:38 errors:0 dropped:0 overruns:0 frame:0
          TX packets:38 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:2883 (2.8 KB)  TX bytes:2883 (2.8 KB)

te@te-VirtualBox:/etc/ssh$
```

So it looks like the the IP address is 10.0.2.15.  This has been assigned by VirtualBox, which is acting like a router.

Next is the server ([help](https://help.ubuntu.com/lts/serverguide/openssh-server.html)).

#### Server setup

We generated a key pair for the server above.  We will get around to using that for authentication, but for the time being we will just use the password for our account on Ubuntu.  We don't need to do anything about credentials for that.

To set up the server, we need to edit ``/etc/ssh/sshd_config``.  First save a copy as ``sshd_config.orig``.

For reference here is the whole file as I have it now.

```
te@te-VirtualBox:/etc/ssh$ sudo cp sshd_config sshd_config.orig
te@te-VirtualBox:/etc/ssh$ ls
moduli            ssh_host_dsa_key.pub      ssh_host_rsa_key
ssh_config        ssh_host_ecdsa_key        ssh_host_rsa_key.pub
sshd_config       ssh_host_ecdsa_key.pub    ssh_import_id
sshd_config.orig  ssh_host_ed25519_key
ssh_host_dsa_key  ssh_host_ed25519_key.pub
te@te-VirtualBox:/etc/ssh$ cat sshd_config
# Package generated configuration file
# See the sshd_config(5) manpage for details

# What ports, IPs and protocols we listen for
Port 22
# Use these options to restrict which interfaces/protocols sshd will bind to
#ListenAddress ::
#ListenAddress 0.0.0.0
Protocol 2
# HostKeys for protocol version 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
#Privilege Separation is turned on for security
UsePrivilegeSeparation yes

# Lifetime and size of ephemeral version 1 server key
KeyRegenerationInterval 3600
ServerKeyBits 1024

# Logging
SyslogFacility AUTH
LogLevel INFO

# Authentication:
LoginGraceTime 120
PermitRootLogin prohibit-password
StrictModes yes

RSAAuthentication yes
PubkeyAuthentication yes
#AuthorizedKeysFile	%h/.ssh/authorized_keys

# Don't read the user's ~/.rhosts and ~/.shosts files
IgnoreRhosts yes
# For this to work you will also need host keys in /etc/ssh_known_hosts
RhostsRSAAuthentication no
# similar for protocol version 2
HostbasedAuthentication no
# Uncomment if you don't trust ~/.ssh/known_hosts for RhostsRSAAuthentication
#IgnoreUserKnownHosts yes

# To enable empty passwords, change to yes (NOT RECOMMENDED)
PermitEmptyPasswords no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Change to no to disable tunnelled clear text passwords
#PasswordAuthentication yes

# Kerberos options
#KerberosAuthentication no
#KerberosGetAFSToken no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes

# GSSAPI options
#GSSAPIAuthentication no
#GSSAPICleanupCredentials yes

X11Forwarding yes
X11DisplayOffset 10
PrintMotd no
PrintLastLog yes
TCPKeepAlive yes
#UseLogin no

#MaxStartups 10:30:60
#Banner /etc/issue.net

# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

Subsystem sftp /usr/lib/openssh/sftp-server

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM yes
te@te-VirtualBox:/etc/ssh$
```
     
The server will listen on port 22.

According to the guide, we should do this:  uncomment ``PasswordAuthentication`` (for now)

```
PermitRootLogin no
ChallengeResponseAuthentication yes
PasswordAuthentication yes   # we'll set it to no eventually
```

x We do the edit with ``nano`` and use ``sudo``
We do the edit with ``vi``

```
sudo vi sshd_config
```

Sometimes the arrow keys don't work.  At moment they are.  Basic vi commands [here](https://www.cs.colostate.edu/helpdocs/vi.html).

- i    insert text before cursor
- a    append text after cursor
- x    delete single character under cursor
- dd   delete entire line

``<esc>`` gets you out of edit mode, then commit your edit with ``:wq``.  Use ``cat`` to check your edit.

I left the RootLogin as it is for the moment.  I'll come back to this.

At this point in my previous work I did  ``sudo /etc/init.d/ssh restart`` but I am following other notes now and I did something else

```
te@te-vb:/etc/ssh$ sudo service ssh restart
```
    
#### Login from Ubuntu

Now, let's try  to connect to ``10.0.2.15`` on port 22

I use my Ubuntu password:

```
te@te-VirtualBox:/etc/ssh$ sudo service ssh restart
te@te-VirtualBox:/etc/ssh$ ssh te@10.0.2.15
The authenticity of host '10.0.2.15 (10.0.2.15)' can't be established.
ECDSA key fingerprint is SHA256:8PEWkTXlIzbYhsmL81npx3nUOS16LFjWbH6bDWMOH2M.
Are you sure you want to continue connecting (yes/no)? y
Please type 'yes' or 'no': yes
Warning: Permanently added '10.0.2.15' (ECDSA) to the list of known hosts.
Password: 
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.10.0-28-generic x86_64)
...

te@te-VirtualBox:~$ ls
Desktop    Downloads         Music     Public     Videos
Documents  examples.desktop  Pictures  Templates
te@te-VirtualBox:~$ 

```

I confirmed the fingerprint above.

And that's a match.

#### Login from OS X:

I had a long write-up about this from a few years ago (link), the short answer is we need to power down Ubuntu then do

```
> VBoxManage modifyvm Ubuntu --natpf1 "guestssh,tcp,,2222,10.0.2.15,22"
```

from macOS, then power Ubuntu up again.

We need to [specify the port](http://stackoverflow.com/questions/5906441/how-to-ssh-to-a-virtualbox-guest-externally-through-a-host
) for the login like so:

```
> ssh -p 2222 te@127.0.0.1
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256:LL3xNhTXTUtOygHWsTdK18ScJRHnVIc222/Z0N/qWlE.
Please contact your system administrator.
Add correct host key in /Users/telliott_admin/.ssh/known_hosts to get rid of this message.
Offending RSA key in /Users/telliott_admin/.ssh/known_hosts:8
RSA host key for [127.0.0.1]:2222 has changed and you have requested strict checking.
Host key verification failed.
> 
```

This ip address is provided by VirtualBox.

We're making progress but the login failed.  The reason is that I haven't updated the key on macOS for the Ubuntu host.

I need to remove the old key from ``/Users/telliott_admin/.ssh/known_hosts``.

I remove these two keys, just with TextEdit

```
127.0.0.1 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDO4Kjzv0/I+TPblXy6jH+McDHp7Y1ETC7XhQ32FWlKDSTQ8+tm4dJrKaTR/8IYGc7Edp/048wZ2xHWrgbsNH9D8+NRsGvoHT5H0wAWD6rYSbG+YMcNyAmWKRuL6vnZrZXVdYeIR4IAqUvGTAOwFdEqZaPUBwWnavlHn81PMHNzPkKevAqopEgHha7GgntAMs04WwCkMTHbSCCj6ORtTLDbhHdUSQiJhK8La1vwJSvSRvqU+3oSZS5dq5+jDxAH1DfLLA+Vgxj+Lpfq4VnVvy0zOM4V6kC5vEhAGiwd0bf6jdU2EjZRVaHNvLRjYEq7Hp2QRXFz3y37JF1Y64ygWkGD

[127.0.0.1]:2222 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDNRSUy/01keRBX2tP0HEjwdQ3wfW5n2aB98FqncZo5VVHoyFfkUsSrxnTKZP7+yo4s7Voty99epDBqn6Vk+U5kZ3eYUJ3fbrJInuX+75sZvLmsMPaj2Yb7SjIkA3mtQiqdjmXWxGwBaDNjzSss6wfq05FfmniB+RqwzgNy6RBnse7yW5TFU8x+s9i1H7fOZg4nISKgF8SlPM4pE7v+PEirTl3A8o2RgQsPaTnck3z2bafRCmfJUd2XwUoAd0gcbpKTbksflBxSLmgLDa1FMznTqAc2QfC00J7qwGcyqP96J56kwFTv5JpN3r79TEU+RmUwD34/j7OB+/SN0jnKoTqp
```

Try again

```
> ssh -p 2222 te@127.0.0.1
The authenticity of host '[127.0.0.1]:2222 ([127.0.0.1]:2222)' can't be established.
ECDSA key fingerprint is SHA256:8PEWkTXlIzbYhsmL81npx3nUOS16LFjWbH6bDWMOH2M.
Are you sure you want to continue connecting (yes/no)? y
Please type 'yes' or 'no': yes
Warning: Permanently added '[127.0.0.1]:2222' (ECDSA) to the list of known hosts.
Password: 
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.10.0-28-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

262 packages can be updated.
133 updates are security updates.

Last login: Tue Jan 16 12:18:12 2018 from 10.0.2.15
te@te-VirtualBox:~$ 

```

I entered my Ubuntu password.  We're in!


according to the manual

    With the above example, all TCP traffic arriving on port 2222 on any host interface will be forwarded to port 22 in the guest. The protocol name tcp is a mandatory attribute defining which protocol should be used for forwarding (udp could also be used). The name guestssh is purely descriptive and will be auto-generated if omitted. The number after --natpf denotes the network card, like in other parts of VBoxManage.
    
To remove this forwarding rule again, use the following command:

```
VBoxManage modifyvm "VM name" --natpf1 delete "guestssh"
```

    If for some reason the guest uses a static assigned IP address not leased from the built-in DHCP server, it is required to specify the guest IP when registering the forwarding rule:

Now what we need to do is to change the authentication so that Ubuntu accepts a key rather than have us type in a password.
