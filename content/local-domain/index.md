---
title: "Setup local domain with self-signed SSL certificate"
date: 2023-04-18T22:51:16-04:00
tags:
  - devops 
---

# Objective

The local domain are helpful to setup the connection between the frontend and backend on the development machine.
It is also a common solution when the actual address and port is difficult to remember.
In this post, I will explain how to set up the local domain and config the SSL certificate on the MacOS laptop.

# Softwares required

In order to config the local domain name and issue the SSL cert, we'll need these tools installed:

- openssl
- MacOS Keychains app
- Nginx: for configuring the proxy
- Admin access
- vi: to edit the host and create the configuration files

The nginx and vi are just my preference, you can use VSCode, emacs for editor and Apache, HA proxy for the proxy.

# Create the local domain

Setup the domain is the most simple task, you can open the hosts file with the admin privillege.

```sh
  sudo vi /etc/hosts
```

Add a line for the new domain and map it to **127.0.0.1**

```
127.0.0.1 mydomain.local
```

If you run the server inside a virtual network, for instance by running the proxy in the Virtual box or Docker, then use the IP of the virtual network interface instead.

After that, you may flush the DNS cache by using this command:

```
sudo dscacheutil -flushcache
```

# Create the Root Certificate Authority (Root CA)

The SSL in the normal website are issued by a trusted Certificate Authority. However, for the local development, we don't need to purchase a SSL (and we don't own the domain), hence we have to setup the Root CA as well. During the handshake step, the browser checks whether the cert is valid by asking the Root CA about the cert, and if the Root CA doesn't confirm with the browser, the browser marks the site as unsafe.

To generate the Root CA, we can use the **openssl** tool.
The steps are:
1. Generate the root CA key
2. Generate the root CA cert

Personally, I suggest to create a directory to store all the files that are created during this process together, I usually create one in `/tmp/ssl` for this purpose. All the files we create after this are in the same directory.

To start with, we have to create the RootCA configuration file.

```
# fileName: config.conf

[req]
prompt = no
distinguished_name = req_distinguished_name
req_extensions = v3_req

[req_distinguished_name]
C = CA
ST = Kitchener
L = ON
OU = Org
CN = mydomain.local
emailAddress = dev@mydomain.local

[v3_req]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = mydomain.local
DNS.2 = api.mydomain.local
DNS.2 = www.mydomain.local
```
Please modify the content of the file to match with your information and the local domain.
The file references and examples can be found from [IBM openSSL configuraton](https://www.ibm.com/docs/en/hpvs/1.2.x?topic=reference-openssl-configuration-examples) and [OpenSSL document](https://www.openssl.org/docs/manmaster/man5/config.html)

After creating these 2 config files, we can generate the rootCA key and cert with these 2 commands

```
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.pem -config config.conf
```

The values of each parameters can be altered by different values, such as the number of days can be change to 365, or the algorithm can be set to sha512. For the details of each parameters, checkout the OpenSSL manual.

# Issue the Self-signed SSL cert

Create the ext file in the same directory with the content like below

```
# filename: mydomain.local.ext 

authoritykeyIdentifier = keyid,issuer
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = mydomain.local
DNS.2 = api.mydomain.local
DNS.2 = www.mydomain.local
```

After this, we can generate the cert key file and the cert signing request file

```
openssl genrsa -out mydomain.local.key 2048
openssl req -new -key mydomain.local.key -out mydomain.local.csr -config config.conf
```

The last step is to issue the cert

```
openssl x509 \
        -in mydomain.local.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial \
        -out mydomain.local.crt -days 1024 -sha256 -extfile mydomain.local.ext
```

After that, you should have the the cert (and other files) in the temporary directory. We don't need the config file as well as the ext file.
A common practice I usually take is to move these files to a more permanent place, such as `/etc/ssl`, so the files aren't gone after reboot.
To import the cert to the Apple Keychains, we can either use the Keychains UI or by running a command like below

```
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain /etc/ssl/rootCA.pem

```

Noted that we don't have to import the cert for the local domain name.

# Config the proxy

We can configure the SSL cert for the website from the proxy level instead of the app server. However, if you prefer to configure the cert from the app, please skip this secion.

I prefer to use Nginx for the proxy because it is easy to setup and the document is excellent.

Here is how the nginx config look like

```
#filename: /opt/homebrew/etc/nginx/servers/mydomain.local.conf

upstream frontend {
   server localhost:3000;
   keepalive 100;
}

upstream backend{
   server localhost:8888;
   keepalive 100;
}

server {
  listen 443 ssl;
  server_name mydomain.local www.mydomain.local;

  ssl_certificate     /etc/ssl/mydomain.local.crt;
  ssl_certificate_key /etc/ssl/mydomain.local.key;

  # location config

  location / {
    proxy_http_version 1.1;
    proxy_pass http://frontend/;
  }

  location ~ /uploads/(file|image) {
    proxy_http_version 1.1;
    client_max_body_size 50M;
    proxy_pass http://frontend/;
  }
}

server {
  listen 443 ssl;
  server_name api.mydomain.local

  ssl_certificate     /etc/ssl/mydomain.local.crt;
  ssl_certificate_key /etc/ssl/mydomain.local.key;

  # location config

  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Access-Control-Allow-Origin *;
    proxy_http_version 1.1;
    proxy_pass http://backend/;
  }
}

```

With Nginx, we can setup complicated routing rule to for the website traffic and configure the SSL without hassle.

A pro tips: If nginx is installed through **brew**, we can use the **brew services** to make it starting automatically when the laptop is boosted.

# Recap

This short post is a summary for the steps to setup the local domain on the local machine.
I hope you find it useful for your works.
