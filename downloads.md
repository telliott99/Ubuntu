#### Downloads

One way to do that would be to find an old laptop with Windows and wipe it.  But I don't have one.  It is possible to "dual boot" OS X and Linux, but it's a pain, from what I understand.  

Another idea is to try to run Linux on my current laptop, by installing a virtual machine.  This is the path that I took when I studied Linux previously.

While we do that we will set up a server.  We are not going to serve pages to the web at large, but we will serve them to machines on our local network.

There is free software called VirtualBox (owned by Oracle) which can be used to provide the VM.  I refer to the program as [VB](https://www.virtualbox.org).

The downloads page is [here](https://www.virtualbox.org/wiki/Downloads).  The current version is 5.2.4. Installation is standard.

On the website is a [page](https://www.virtualbox.org/download/hashes/5.2.4/SHA256SUMS) with digests for different files including ``VirtualBox-5.2.4-119785-OSX.dmg``.  The size is only 95 MB.

```
30ffeea2f92fa6e649f9004859f1ef318368e6876d9a74e8c644a3110af23ddf
30ffeea2f92fa6e649f9004859f1ef318368e6876d9a74e8c644a3110af23ddf
```

The first line above is from the website, the second is from getting the digest on the file I downloaded:

```
> openssl dgst -sha256 /Users/telliott_admin/MyDownloads/VirtualBox-5.2.4-119785-OSX.dmg 
SHA256(/Users/telliott_admin/MyDownloads/VirtualBox-5.2.4-119785-OSX.dmg)= 30ffeea2f92fa6e649f9004859f1ef318368e6876d9a74e8c644a3110af23ddf
>
```

#### Ubuntu

If we go to [Ubuntu](https://www.ubuntu.com/desktop) 

We are offered "Ubuntu 16.04.3 LTS".  The file is ``ubuntu-16.04.3-desktop-amd64.iso``.  The size is 1.59 GB.

Do not worry about the ``AMD`` part.  It's the right file for our VM.

Instructions for how to obtain the digest from the website are [here](https://tutorials.ubuntu.com/tutorial/tutorial-how-to-verify-ubuntu?_ga=2.234723934.1538887254.1516041974-1417812063.1516041974#0).

Unfortunately, they think I should learn how to use GPG to do that.  I don't deny that's probably a great idea.

But I don't want to play with gpg right now so I just click on this [page](http://releases.ubuntu.com/16.04/?_ga=2.234681310.1538887254.1516041974-1417812063.1516041974) on the [link](http://releases.ubuntu.com/16.04/SHA256SUMS).

The first line on that page is the first line here:

```
1384ac8f2c2a6479ba2a9cbe90a585618834560c477a699a4a7ebe7b5345ddc1
1384ac8f2c2a6479ba2a9cbe90a585618834560c477a699a4a7ebe7b5345ddc1
```

The second line is the result of this command in Terminal:
```
> openssl dgst -sha256 /Users/telliott_admin/MyDownloads/ubuntu-16.04.3-desktop-amd64.iso
```

That one takes almost a minute.  We have a match so we should be good to go.

We'll looking into using gpg later.  It involves getting the public key of the server and using to check the signature on the file with the digests.

```
brew install gpg
```

A huge number of dependencies.  Just saying.

Following the [instructions](https://tutorials.ubuntu.com/tutorial/tutorial-how-to-verify-ubuntu?_ga=2.234723934.1538887254.1516041974-1417812063.1516041974#0).



