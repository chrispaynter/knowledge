# Generate SSL Certiciates on Linux

## Step 1: Download Let's Encrypt
Head to the opt folder. This is a folder on Linux that is used to store software that isn't part of the default installation.
`cd /opt`

Clone down the lets encrypt repo from GitHub
`git clone https://github.com/lukas2511/letsencrypt.sh`

Change into the letsencrypt directory
`cd letsencrypt.sh`

## Step 2: Configure your domains
There are two files that we need to create to generate our certificates. 

### domains.txt
The first one we create is domains.txt which is used for configuring the domains we want to create SSL certificates for.
`touch domains.txt`

Edit the file to insert your domains. I use VIM.
`vim domains.txt`

Inside VIM, press the `i` key and then type your domains in with spaces.
`yourdomain.com`

When you're done press the `esc` key, then type `:wq` to write your changes and quit.

### config
The next file we need to create is the config file. This file is, if you didn't guess, the configuration that letsencrypt will use when it runs.

Following the same process as for domains.txt
`touch config`
`vim config`

Enter the following into the vim editor and write and quit as before.
`WELLKNOWN=/var/www/html/.well-known/acme-challenge`

Next we'll explain what this *well known* thing is.

## Step 3: Setup your well known folder
[This site](http://letsencrypt.readthedocs.io/en/latest/using.html) explains what well known is:
>The webroot plugin works by creating a temporary file for each of your requested domains in ${webroot-path}/.well-known/acme-challenge. Then the Let’s Encrypt validation server makes HTTP requests to validate that the DNS for each requested domain resolves to the server...

What this means is that the let's encrypt server needs to ping back to your server to make sure that you do indeed own the domain that you're creating a certificate for. If you didn't, there'd be no way you could point the domain name to the server that the Let's Encrypt challenge resides on.

Don't really fret too much about all that, all you really need to do here is create the folder and make sure that it's accessible from the web. This will depend on the web server you are running.

`mkdir /var/www/html/.well-known/acme-challenge`

## Step 4: Run Let's Encrypt
Now all we need to do is run Let's Encrypt. You should still be in the `/opt/letsencrypt.sh/` directory so all you need to do now is run `./letsencrypt.sh -c`

You should then see something like this, where `yourdomain.com` is whatever domain you put into the `domains.txt` file:
```
# INFO: Using main config file /opt/letsencrypt.sh/config
Processing yourdomain.com
 + Signing domains...
 + Generating private key...
 + Generating signing request...
 + Requesting challenge for yourdomain.com...
 + Responding to challenge for yourdomain.com...
 + Challenge is valid!
 + Requesting certificate...
 + Checking certificate...
 + Done!
 + Creating fullchain.pem...
 + Done!
```

## Step 5: Use your certificates
Your domains are now created and should be located at `/opt/letsencrypt.sh/cert/yourdomain.com/`

THere will be a few files in here, some you don't need to worry about (the ones that have numbers appended to them. There's a number of formats in SSL, I won't go into it now but you can [read more here](http://serverfault.com/questions/9708/what-is-a-pem-file-and-how-does-it-differ-from-other-openssl-generated-key-file).

The one's you'll care about, depending on the server setup you have

- _cert.pem_ - The server certificate that gets sent to the client, such as a web browser.
- _privkey.pem_ - The secret key that should be secured on your server.
- _chain.pem_ - intermediates
- _fullchain.pem_ - server cert + intermediates


## Conclusion
You now have certificates for your domain and can move on to setting up your server. In the future I hope to link instructions for different server environments below.

