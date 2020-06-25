# nginx-mutual-ssl
Configuring mutual SSL (or two way ssl) for NGINX

## Pre-requisite
Certificates (.crt), key (.key), certificate signing authority are required to configure mutual ssl. Openssl will be used to create these files. Section below lists the command to create the necessary files.

### Generation of CA key & certificate  
    openssl genrsa -des3 -out ca.key 4096
    openssl req -new -x509 -days 5000 -key ca.key -out ca.crt

### Generation of user/client key & certificate creation
    openssl genrsa -des3 -out user.key 4096
    openssl req -new -key user.key -out user.csr
    openssl x509 -req -days 365 -in user.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out user.crt
    openssl pkcs12 -export -out user.pfx -inkey user.key -in user.crt -certfile ca.crt

### Generation of site/server (hellomssl.com) key & certificate creation.
    openssl genrsa -out hellomssl.com.key 4096
    openssl req -new -key hellomssl.com.key -out hellomssl.com.csr
    openssl x509 -req -days 365 -sha256 -in hellomssl.com.csr -CA ca.crt -CAkey ca.key -set_serial 1 -out hellomssl.crt

## nginx configuration
CA & server side ceritificates are expected in pre-defined path in nginx configuration. So, the certificates created are expected in defined directory paths.

## Deployment
Nginx is deployed as docker container. Refer docker-compose.yml to understand the deployment orchestration. Pretty self-explanatory it is !

## Verification
### Curl to test from localhost
Add host entry for 'hellomssl.com'
curl --cert ./user/user.crt:manage --key ./user/user.key https://hellomssl.com:8443  --cacert ./ca/ca.crt


