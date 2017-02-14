---
published: false
---
DVWS is similar to Damn Vulnerable Web Application, but communication between client and server is based on WebSockets. Source code is available [here](https://github.com/interference-security/DVWS) , but it's much easier to use prepared [Docker image](https://hub.docker.com/r/tssoffsec/dvws/).

###WebSocket proxy:
Brute force and SQL injection required some automatization to get results quickly. For Error SQL Injection I wrote my own WebSocket client in JavaScript, but for Brute force and Blind SQL Injection I took different approach. I wanted to use Hydra and sqlmap, but I wasn't able to connect to WebSocket. To solve this problem I wrote proxy which allows for communication between HTTP and WebSocket protocols. It's an application written in Java with Jetty (HTTP server) and Tyrus (WebSocket client) libraries. Tools can make HTTP requests which are transfered to vulnerable WebSocket application. Project is available on [GitHub](https://github.com/mmmds/WebSocketProxy).

##Brute Force 
To log in we send JSON with login and password encoded by base64. In case of failure we receive
    <pre>Incorrect username/password</pre>

I downloaded wordlist with most common logins and passwords, then I converted these words into their base64 form. 
    while read line; do echo $line | base64; done < passwords.txt > b64passwords.txt
    
I ran WebSocket proxy and Hydra to perform brute force attack. It was important to set -t 1 because this proxy doesn't support parallel connections.
In result I got login and password "YWRtaW4=" what decodes to "admin"
![]({{site.baseurl}}/images/dvws_brute_force_2.png)
![]({{site.baseurl}}/images/dvws_brute_force_1.png)

##CSRF
There is no CSRF protection, so we can prepare our [own website](https://github.com/mmmds/walkthroughs/blob/master/dvws/csrf.html) with script which will connect to WebSocket and send password change request. Request consists of JSON with two password fields. Second field 'cpass' is not even checked, so we can skip it. Victim must be logged in, otherwise they will get error "Message Authenticated session is required for changing password.".