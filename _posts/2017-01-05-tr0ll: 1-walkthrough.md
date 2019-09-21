---
published: true
layout: post
---
According to VM's description, we have to get root and read /root/Proof.txt file. 


This VM can be found [here](https://www.vulnhub.com/entry/tr0ll-1,100/).

1. First thing after running VM was to get its IP address. I used nmap to examine my local network.
![]({{site.baseurl}}/images/tr0ll1_1.png)
I found IP and information that it runs FTP, SSH and HTTP services.

2. I started with HTTP but I found nothing more than irritating images. Then I moved to FTP what gave me a file with captured network packets.
![]({{site.baseurl}}/images/tr0ll1_2.png)

3. I ran Wireshark and loaded this file. While I was analyzing packets, one of them caught my attention. I didn't want to believe that it was another worthless step so I tried to retrieve some information from it. 
![]({{site.baseurl}}/images/tr0ll1_3.png)
Phrase "sup3rs3cr3tdirlol" appeared to be a directory name, accessible through HTTP and with a single file inside. 

5. This single file called "roflmao" was binary. Firstly, I tried to analyze what this binary does. I didn't find anything in its instructions, but one string seemed to be intereseting. 
![]({{site.baseurl}}/images/tr0ll1_4.png)

6. I continued analyzing but there was nothing at address 0x0856BF. It blocked me for a long time and then I thought that maybe I should check outside the binary. This address appeared to be valid, but in the browser and finally it gave me an access to another directory. I found potential username and passwords there.
![]({{site.baseurl}}/images/tr0ll1_5.png)

7. Hint about username was false unlike the hint about password, which was too literal to take it seriously. It took me some time but finally I found correct combination.
![]({{site.baseurl}}/images/tr0ll1_6.png)

8. I logged in through SSH but still I had to get root. I used OS version from welcome screen to find exploit https://www.exploit-db.com/exploits/37292. It worked without any problems and I got root in just a moment.
![]({{site.baseurl}}/images/tr0ll1_7.png)
		
        	
            
After I finished, I checked another solutions and found [more sophisticated way to get root](http://maep.dk/?id=bac36d54b96621008d5d6dbbfe8a2a64)
