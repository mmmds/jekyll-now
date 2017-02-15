---
published: true
---
DVWS is similar to Damn Vulnerable Web Application, but communication between client and server is based on WebSockets. Source code is available [here](https://github.com/interference-security/DVWS) , but it's much easier to use prepared [Docker image](https://hub.docker.com/r/tssoffsec/dvws/).

### WebSocketProxy:
Brute force and SQL injection required some automatization to get results quickly. For Error SQL Injection I wrote my own WebSocket client in JavaScript, but for Brute force and Blind SQL Injection I took different approach. I wanted to use Hydra and sqlmap, but I wasn't able to connect to WebSocket. To solve this problem I wrote proxy which allows for communication between HTTP and WebSocket protocols. It's an application written in Java with Jetty (HTTP server) and Tyrus (WebSocket client) libraries. Tools can make HTTP requests which are transfered to vulnerable WebSocket application. Project is available on [GitHub](https://github.com/mmmds/WebSocketProxy).

## Brute Force 
To log in we send JSON with login and password encoded by base64. In case of failure we receive
		
	<pre>Incorrect username/password</pre>

I downloaded wordlist with most common logins and passwords, then I converted these words into their base64 form. 
    
    while read line; do echo $line | base64; done < passwords.txt > b64passwords.txt
    
I ran WebSocket proxy and Hydra to perform brute force attack. It was important to set -t 1 because this proxy doesn't support parallel connections.
In result I got login and password "YWRtaW4=" what decodes to "admin"
![]({{site.baseurl}}/images/dvws_brute_force_2.png)
![]({{site.baseurl}}/images/dvws_brute_force_1.png)

## CSRF
There is no CSRF protection, so I prepared [website](https://github.com/mmmds/walkthroughs/blob/master/dvws/csrf.html) with script which will connect to WebSocket and send password change request. Request consists of JSON with two password fields. Second field 'cpass' is not even checked, so we can skip it. Victim must be logged in, otherwise they will get error "Message Authenticated session is required for changing password.".

## File Inclusion
In request filename to display is sent. I used Owasp ZAP to intercept request and put filename I wanted to display.
![]({{site.baseurl}}/images/dvws_file_inclusion.png)

## Error SQL Injection
Both login and password are vulnerable. This vulnerability reveals error message in case of invalid query. I wrote [my own client](https://github.com/mmmds/walkthroughs/blob/master/dvws/error_sql.html) in JavaScript which uses group by with having technique to get names of tables and columns and dump table users. This technique casues error in which some part of query is executed and returned to interface.

    admin admin
    bob bobbuilder
    jsmith password

    comments Name
    comments Comment
    users username
    users first_name
    users last_name
    users password
    cond_instances NAME
    cond_instances OBJECT_INSTANCE_BEGIN
    events_waits_current THREAD_ID
    events_waits_current EVENT_ID
    events_waits_current EVENT_NAME
    events_waits_current SOURCE
    events_waits_current TIMER_START
    events_waits_current TIMER_END
    events_waits_current TIMER_WAIT
    events_waits_current SPINS
    events_waits_current OBJECT_SCHEMA
    events_waits_current OBJECT_NAME
    events_waits_current OBJECT_TYPE
    
   
## Blind SQL Injection
It's similar to Error SQL Injection but this time there is no result from query. I decided to use sqlmap combined with WebSocketProxy to dump users table.
![]({{site.baseurl}}/images/dvws_blind_3.png)
![]({{site.baseurl}}/images/dvws_blind_1.png)
![]({{site.baseurl}}/images/dvws_blind_2.png)

## Command Execution

## Reflected XSS

## Stored XSS
These vulnerabilities are nothing special in comparison with DVWA. Standard payloads work for them.

	127.0.0.1; cat /etc/passwd

	<img src=x onerror="alert(1)">
