# stunnel docker image

## Description

The stunnel program is designed to work as an SSL encryption wrapper between
remote client and local (inetd-startable) or remote server. It can be used to
add SSL functionality to commonly used inetd daemons like POP2, POP3, and IMAP
servers without any changes in the programs' code. Stunnel uses the OpenSSL
library for cryptography, so it supports whatever cryptographic algorithms are
compiled into the library.

This image allows you to run stunnel in a completelly containerized environment

## How to use this image

First generate the certificates you're going to use:

```
openssl genrsa 2048 > stunnel.key
openssl req -new -x509 -key stunnel.key -out stunnel.crt -days 1095
cat stunnel.crt stunnel.key > stunnel.pem
```

Once the certificate has been created, you can run stunnel passing the config
file over stdin.

In this example we're going to link two containers and run BitlBee over ssl
```
# run bitlbee and keep it in background
docker run -d --name bitlbee fr3nd/bitlbee -n

# run stunnel
echo "
cert=/etc/ssl/stunnel.pem
foreground=yes
[bitlbee]
accept=0.0.0.0:6697
connect=bitlbee:6667
" |  docker run \
  --rm \
  -i \
  -p 6697:6697 \
  --link=bitlbee:bitlbee \
  -v $(pwd)/stunnel.pem:/etc/ssl/stunnel.pem:ro \
  fr3nd/stunnel -fd 0
```
