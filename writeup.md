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

** HERE EXPLAIN ABOUT GOING TO THE WEBSITE, ITS WORDPRESS, CLICKING AROUND IN THE WEBSITE, DIRBUSTER BUT TO NO AVAIL **

After a bit of research about the best hacking tools for WordPress I came accross a tool called ```wpscan``` . There are many tutorials but I referred to https://www.youtube.com/watch?v=Z9QPazbfwFE in order to learn the basics. So, after you register in http://wpscan.com and get your token, you can do some real enumeration. Run the following command (with your own wpscan API token):
```
wpscan --url metapress.htb --enumerate --api-token <API TOKEN>
```
A lot of useful information comes up. Some of the highlights:
```
[+] admin
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://metapress.htb/wp-json/wp/v2/users/?per_page=100&page=1
 |  Rss Generator (Aggressive Detection)
 |  Author Sitemap (Aggressive Detection)
 |   - http://metapress.htb/wp-sitemap-users-1.xml
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)
```
Now we know there is an author account. The output also contains info that this WP version is vulnerable and it should list a handful of vulnerabilities.
