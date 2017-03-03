---
published: false
---
I chose one application in Google Play to test. I decompiled it using tools dex2jar and JD-GUI. Firstly I connected my phone to computer to get apk file

	$ adb pull /data/app/com.example.app.apk

Then I converted apk into jar and ran decompiler

	$ ./d2j-dex2jar.sh com.example.app.apk -o app.jar
	dex2jar com.example.app.apk -> app.jar

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

