# InstallCert.java

Java program written by Andreas Sterbenz and [posted on a blog in Oct, 2006](http://web.archive.org/web/20061108195331/http://blogs.sun.com/andreas/entry/no_more_unable_to_find). Link to Java program in Andreas' blog no longer works, but [the source was linked in another blog](https://web.archive.org/web/20190831085142/http://nodsw.com/blog/leeland/2006/12/06-no-more-unable-find-valid-certification-path-requested-target).


## Usage

Need to compile, first:
```
javac InstallCert.java
```

>**Note** since Java 11, you can run it directly without compiling it first:

```
java --source 11 InstallCert.java <args>
```


### Access server, and retrieve certificate (accept default certificate 1)

```
java InstallCert [--proxy=proxyHost:proxyPort] <host>[:port] [passphrase]
```


### Extract certificate from created jssecacerts keystore

```
keytool -exportcert -alias [host]-1 -keystore jssecacerts -storepass changeit -file [host].cer
```


### Import certificate into system keystore

```
sudo keytool -importcert -alias [host] -keystore [path to system cacerts] -storepass changeit -file [host].cer
```

Hint: `keystore` system cacerts path should be located in `$JAVA_HOME/lib/security/cacerts` if your `$JAVA_HOME` env var is set.

>**Note** since Java 11, you can use the `-cacerts` flag instead of `-keystore [cacerts path]`

```
sudo keytool -importcert -alias [host] -cacerts -storepass changeit -file [host].cer
```


#### Example

```
java InstallCert woot.com:443

    Loading KeyStore /usr/lib/jvm/java-6-sun-1.6.0.26/jre/lib/security/cacerts...
    Opening connection to woot.com:443...
    Starting SSL handshake...

    javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target

    <...>

    Server sent 1 certificate(s):

     1 Subject O=Woot Inc, C=US, ST=Texas, L=Carrollton, CN=*.woot.com
       Issuer  CN=SecureTrust CA, O=SecureTrust Corporation, C=US
       sha1    4b 46 ca 6b 83 05 b3 51 ff c6 e7 9c fd b3 9b e3 3f 2e c4 53 
       md5     e8 a5 88 1b d5 67 bb fc 88 cc b1 c5 2b ac c4 7d 

    Enter certificate to add to trusted keystore or 'q' to quit: [1]

[enter]

    [
    [
      Version: V3
      Subject: O=Woot Inc, C=US, ST=Texas, L=Carrollton, CN=*.woot.com
      Signature Algorithm: SHA1withRSA, OID = 1.2.840.113549.1.1.5

    <...>

    Added certificate to keystore 'jssecacerts' using alias 'woot.com-1'

keytool -exportcert -alias woot.com-1 -keystore jssecacerts -storepass changeit -file woot.com.cer

    Certificate stored in file <woot.com.cer>
  
sudo keytool -importcert -alias woot.com -cacerts -storepass changeit -file woot.com.cer

    Owner: O=Woot Inc, C=US, ST=Texas, L=Carrollton, CN=*.woot.com
    Issuer: CN=SecureTrust CA, O=SecureTrust Corporation, C=US
  
    <...>
  
    Trust this certificate? [no]:


yes

    Certificate was added to keystore
```
访问ssl报错，解决方案 
https://blog.csdn.net/Tomcat_Lu/article/details/128199725
```
2023-07-18 10:04:57 [com.alibaba.nacos.client.Worker.longPolling.fixed-portal.windmagics.com-48b1c6c4-018e-4fa3-8c9e-20d163a013fe] ERROR c.a.n.c.c.i.ClientWorker - longPolling error : 
javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.ssl.Alert.createSSLException(Alert.java:131)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:326)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:269)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:264)
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1339)
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.onConsumeCertificate(CertificateMessage.java:1214)
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.consume(CertificateMessage.java:1157)
	at java.base/sun.security.ssl.SSLHandshake.consume(SSLHandshake.java:392)
	at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:444)
	at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:422)
	at java.base/sun.security.ssl.TransportContext.dispatch(TransportContext.java:183)
	at java.base/sun.security.ssl.SSLTransport.decode(SSLTransport.java:171)
	at java.base/sun.security.ssl.SSLSocketImpl.decode(SSLSocketImpl.java:1403)
	at java.base/sun.security.ssl.SSLSocketImpl.readHandshakeRecord(SSLSocketImpl.java:1309)
	at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:440)
	at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:411)
	at java.base/sun.net.www.protocol.https.HttpsClient.afterConnect(HttpsClient.java:567)
	at java.base/sun.net.www.protocol.https.AbstractDelegateHttpsURLConnection.connect(AbstractDelegateHttpsURLConnection.java:185)
	at java.base/sun.net.www.protocol.http.HttpURLConnection.getOutputStream0(HttpURLConnection.java:1367)
	at java.base/sun.net.www.protocol.http.HttpURLConnection.getOutputStream(HttpURLConnection.java:1342)
	at java.base/sun.net.www.protocol.https.HttpsURLConnectionImpl.getOutputStream(HttpsURLConnectionImpl.java:246)
	at com.alibaba.nacos.common.http.client.request.JdkHttpClientRequest.execute(JdkHttpClientRequest.java:106)
	at com.alibaba.nacos.common.http.client.InterceptingHttpClientRequest.execute(InterceptingHttpClientRequest.java:53)
	at com.alibaba.nacos.common.http.client.NacosRestTemplate.execute(NacosRestTemplate.java:482)
	at com.alibaba.nacos.common.http.client.NacosRestTemplate.postForm(NacosRestTemplate.java:407)
	at com.alibaba.nacos.client.config.http.ServerHttpAgent.httpPost(ServerHttpAgent.java:155)
	at com.alibaba.nacos.client.config.http.MetricsHttpAgent.httpPost(MetricsHttpAgent.java:68)
	at com.alibaba.nacos.client.config.impl.ClientWorker.checkUpdateConfigStr(ClientWorker.java:441)
	at com.alibaba.nacos.client.config.impl.ClientWorker.checkUpdateDataIds(ClientWorker.java:408)
	at com.alibaba.nacos.client.config.impl.ClientWorker$LongPollingRunnable.run(ClientWorker.java:596)
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:515)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at java.base/java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:304)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628)
	at java.base/java.lang.Thread.run(Thread.java:834)
Caused by: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:439)
	at java.base/sun.security.validator.PKIXValidator.engineValidate(PKIXValidator.java:306)
	at java.base/sun.security.validator.Validator.validate(Validator.java:264)
	at java.base/sun.security.ssl.X509TrustManagerImpl.validate(X509TrustManagerImpl.java:313)
	at java.base/sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:222)
	at java.base/sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:129)
	at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1323)
	... 31 common frames omitted
Caused by: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.provider.certpath.SunCertPathBuilder.build(SunCertPathBuilder.java:141)
	at java.base/sun.security.provider.certpath.SunCertPathBuilder.engineBuild(SunCertPathBuilder.java:126)
	at java.base/java.security.cert.CertPathBuilder.build(CertPathBuilder.java:297)
	at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:434)
	... 37 common frames omitted

```
