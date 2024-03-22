## GoDaddy SSL Certificate Configs

Steps to get .jks file for tomcat from a GoDaddy SSL certificate

From Godaddy SSL, we get three files

1. certfile.crt
2. keyfile.key
3. cert_bundle.crt

### Step 1

Run the following command

```bash
openssl pkcs12 -export -in /dirname/certfile.crt 
-inkey /dirofkeyfile/keyfile.key -out /destdir/filename.p12 
-name 'aliasname' -CAfile /dirname/cert_bundle.crt 

```

### Step 2

Use the generated .p12 file to generate .jks file using java keytool

```bash
keytool -importkeystore 
-deststorepass "password" 
-destkeypass "password" 
-destkeystore /destdir/filename.jks 
-srckeystore /srcdir/filename.p12 
-srcstoretype PKCS12 
-srcstorepass "password" 
-alias "aliasname"
```

Edit the server.xml file in tomcat as required

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true">
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
                <Certificate certificateKeystoreFile="/dirname/filename.jks"
                        keystorePass="password"
                        clientAuth="false" 
                        sslProtocol="TLS"
                        keyAlias="aliasname"
                        type="RSA"/>
        </SSLHostConfig>
    </Connector>
```