## Discovery
First things's first: we run NMAP. I like to use 
```
$ nmap -A 10.10.11.186
```
to get as much as info about the target machine as possible. 

We find there is a HTTP server on port 80. If you try to access it, you may find that the name cannot be resolved. In this case we need to tell Kali to resolve it locally by running:
```
$ sudo vim /etc/hosts
```
and simply adding the ip address and the name metapress.htb in the file like this:
```
127.0.0.1       localhost
127.0.1.1       kali

10.10.11.186    metapress.htb
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
```
NOTE: If you are inexperienced with ```vim```, its better to use ```nano``` in this case. 

