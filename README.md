# 2 way SSL with spring boot

Example client (nt-gateway) and service (nt-ms) code to show how to get 2 way SSL setup with self signed certificate.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. 

### Prerequisites

* Java 1.8
* Spring boot 2.1.2
* Java keytool utility


### Installing

Download nt-gateway and nt-ms projects in to your STS or Eclipse workspace. 

## What is keystore and truststore  ?
 keystore , as name suggests stores the key (private key, public key and the respective certificates)
 . In one way ssl only server needs to have keystore as it will need to present
 its certificate to the client (certificate is present in the keystore)

 However we need to note that if the server certificate is self signed we (client) wont be able to identify the
 server .... for identifying the server we need to import server's certificate into our truststore

 Truststore, as name suggest store the public certificate of the CA to which it trust .. as I said earlier if we want
to trust the server's self signed certificate we'll need to import it to our trustore !!

### How to generate keystore for our application

This is the command to generate keystore. keystore can have more than one key (private, public/ hence certificates). they are
differentiated from each other by their alias

```
keytool -genkey -alias mykey -keyalg RSA -keypass changeit -keystore keystore.jks 
-storepass changeit
```

here our alias name is mykey and the password for key as well as keystore is same, we can change it later

to list all the certificate inside store (keystore/ truststore)

```
keytool -list -keystore keystore.jks
```
use -v to get more details !!
need password to list the certificates as well

### How to create custom truststore

This command will create truststore for us

```
keytool -genkey -alias exampletruststore -keyalg RSA -keystore mytruststore.jks
```

as you might have guessed it the command to generate keystore and truststore is same !!
it will ask you some details to provide as it might have asked while generating keystore.

This is the command to remove keys stored in the truststore to make it empty

```
keytool -delete -alias exampletruststore -storepass changeit -keystore mytruststore.jks
```

"changeit" is the default password of the java keystore

### How to create certificate(.cer) from keystore(.jks)

```
keytool -export -alias mykey -file mykey.cer -keystore keystore.jks -storepass changeit
```

### How to import your own certificate to your empty truststore

```
keytool -import -alias mykey -file mykey.cer -keystore mytruststore.jks -storepass changeit
```

you can verify using -list as mentioned earlier


### How to tell JVM to use our own truststore instead of using JVM's default truststore "cacerts"

We'll need to set this parameter and give it our trustore password in the command line while running our java application

```
-Djavax.net.ssl.trustStore=mytruststore.jks
-Djavax.net.ssl.trustStorePassword=changeit
```

You can also set this in intellij idea's edit configuration also in the application.yml (I am not sure how to do that I am still exploring !!)

Note : don't store keystore which your application is using on the public repo (as it contains private key, someone might impersonate you and do nasty stuff).