# How to use a self-signed Certificate with a service in Docker

https://stackoverflow.com/questions/3775483/ssl-handshake-exception



## Debugging

### Client doesnt trust the self signed server certificate
#### Client Error
sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target

#### Server Error
Could not accept connection from tcp://172.18.0.1:58978: javax.net.ssl.SSLHandshakeException: Received fatal alert: certificate_unknown

#### Cause
The client does not accept the self-signed presented to it by the server.

#### Solution
Add the self-signed server certificate to the clients trust store
```
> keytool -list -v -keystore server_keystore
> keytool -export -alias server_cert_alias -keystore keystore -rfc -file server_cert.cer
> keytool -import -alias server_cert_alias -file server_cert.cer -keystore client_truststore
```
