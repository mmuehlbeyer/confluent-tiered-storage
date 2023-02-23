# confluent-tiered-storage

## Prerequisits


## Create certs for kes

As mentioned above we need encrypted minio buckets to work with Confluent Tiered Storage.


TLS private key and self-signed cert (adapt the dir  if needed)

```bash

openssl req -x509 -nodes -days 7300 -newkey rsa:2048 -keyout kes/server.key -out kes/server.cert -subj "/C=/ST=/L=/O=/CN=localhost" -addext "subjectAltName = IP:127.0.0.1, DNS:minio-kes"

```

The output should look like:

<details>
  <summary>Details</summary>

```bash
Generating a RSA private key
.......+++++
...............................................................................................+++++
writing new private key to 'server.key'
-----
req: No value provided for Subject Attribute C, skipped
req: No value provided for Subject Attribute ST, skipped
req: No value provided for Subject Attribute L, skipped
req: No value provided for Subject Attribute O, skipped

```
</details>  
  
Next we create a TLS private/public key pair for client authentication.

```bash
docker run -v $PWD/kes:/root minio/kes  identity new --cert /root/client.crt --key /root/client.key myapp
```


Again example output for reference
<details>
  <summary>Details</summary>

```bash

Your API key:

   kes:v1:ACTnWlStnksF1ghlVujO1/Y/DLuSVADNAih6bnCvpqxu

This is the only time it is shown. Keep it secret and secure!

Your Identity:

   2a84d486c770740c4ca256c4ec3aa1458b29a398a1edefca38f408c6a865fd9d

The identity is not a secret. It can be shared. Any peer
needs this identity in order to verify your API key.

The generated TLS private key is stored at: /root/client.key
The generated TLS certificate is stored at: /root/client.crt

The identity can be computed again via:

    kes identity of kes:v1:ACTnWlStnksF1ghlVujO1/Y/DLuSVADNAih6bnCvpqxu
    kes identity of /root/client.crt
```
</details>    

we will need this identity from above for configuring the kes server.


## configure the kes server

copy identy from above to config.yml

start kes with

```
docker-compose -f docker-compose-kes.yml up -d 
```


## create a key for minio
docker exec -it minio-kes ./kes  key create -k my-minio




## start the kafka stack along with minio

adapt minio root user and password if wanted

```bash
docker-compose up -d
```

login to minio webui http://127.0.0.1:9090
