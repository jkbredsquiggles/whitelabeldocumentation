# Video Chat

We currently use Jitsi as a service from 8x8

# Configuration

A public private key pair is required to set up video chat sessions on 8x8.

Instructions are found at https://developer.8x8.com/jaas/docs/api-keys-generate-add

## Summary

Generating your JaaS API key

Your uploaded public key is used to authenticate generated JWT tokens using the Private Key of the specific key pair. When the Jitsi IFrame connects to your meeting, valid JaaS JWTs enable meeting room connections in the AppID as specified by the roomName value. The Private Key is used to sign the Jitsi JWT.

A JaaS API key is a Public Key consisting of a 4096 bits RSA SSH Key Pair in PEM format. You can generate the Key Pair using the ssh-keygen command and specify rsa as the algorithm, 4096 as the size, and PEM as the format. Refer to ssh-keygen - Generate a New SSH Key for more information about this facility.

Open a terminal window, then use the ssh-keygen command:

> ssh-keygen -t rsa -b 4096 -m PEM -f <filename>
The following shows an example output after running the keygen command:

dev$ssh-keygen -t rsa -b 4096 -m PEM -f jaasauth.key
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in jaasauth.key.
Your public key has been saved in jaasauth.key.pub.
The key fingerprint is:
SHA256:aHkvV50wzp6hJ7DB/BTiuzGbh73x3JAnSLhGuxRsyxw dev@dev-machine
The key's randomart image is:
+---[RSA 4096]----+
|                 |
|                 |
|        . . o    |
|       B o + + . |
|      + E o = o  |
|     . * / = +   |
|        #oO B .  |
|       o.Xo* =   |
|        =...o .  |
+----[SHA256]-----+

You must then save the public key in PEM format using the openssl command.

For example:

> openssl rsa -in <filename> -pubout -outform PEM -out <filename>.pub
Another example of the openssl command is as follows:

dev$openssl rsa -in jaasauth.key -pubout -outform PEM -out jaasauth.key.pub
writing RSA key
The following is an example of a saved JaaS API (Public) key file:

dev$cat jaasauth.key.pub
-----BEGIN PUBLIC KEY-----
MIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA1DJxsTKt2lBSl0n4WaqO
xA/lZ+5PeaLMnSPBLku8ZlVjDaU5fUH8oTkoEZovhJGBcR+uWUp8XMfE3l0LWat2
2UfdM4WMm2F8wLKAay/NGrLNQFfcJCn6dEimASVPKKe7wRX1/mcDeb5mAXvRF4u2
iZ3BYw7lLPT6lxtdAwWP+s7sQfsK/7ADoz46o0MZiRlzRDnYtclt2pU4Y6z+fXyF
tMs4bIIWzLxe81rucXLFXi2FVZu4fngTEV91NS4Y+PiQ4sSLPnEJh5TDZLbq6tLS
7qOrnp5tbUS1bPfmRIL38viC+m9wOzPM2Kt8+NEj/HtKg4UlJ4cMS2OB+jGYv//H
zZXfPbzSzn6IEs6C6AH+EEqj0tUTX6wlhi5x6wyaEok20DHq/BgvD4vOwa9eBlBn
I+kw0NT4mjDmv9I2Rc7QbXsrOkPkCNGKsgXdGeJKO2UNNpetEBV5LTYiTjO3sQH7
7WH92ujOg0TouTYvjeuTjkEn8GqWUH6l6UrYw0kbno/WBQGcBFpe4ygmSgznLhGq
3yAuDXoXu7hBHNXTOdyis0aKp+2KOGvoV21gYrU4Z8Uy4JjZNZ1oSwn0Z6RO69tZ
4ni9b++cjjTx0Py0I/U0XvNSneJyXpdjHpOnVQPsLQOVbfBHdSHgNbVaT+18vSeM
9VI7LlqQd0SpRw0S0D9/qgcCAwEAAQ==
-----END PUBLIC KEY-----

Uploading your API Key

Once an RSA key is generated and in the correct format, your JaaS API key can then be added on the JaaS console and a Key Id (kid) will be generated for it. Please refer to the API keys section of the JaaS console for instructions on adding an API key.

