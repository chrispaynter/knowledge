# Generate SSL Certiciates on Linux

Instructions to create SSL certificates for free on your Linux server using [Let's Encrypt](https://letsencrypt.org/).

Note that Let's Encrypt certificates will expire after 90 days, see the very bottom of this for information about why.

## Step 1: Download Let's Encrypt
Head to the opt folder. This is a folder on Linux that is used to store software that isn't part of the default installation.
`cd /opt`

Note if you are on mac you'll need to create the opt folder using `mkdir /opt`

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
Your certificates are now created and should be located at `/opt/letsencrypt.sh/cert/yourdomain.com/`

THere will be a few files in here, some you don't need to worry about (the ones that have numbers appended to them. There's a number of formats in SSL, I won't go into it now but you can [read more here](http://serverfault.com/questions/9708/what-is-a-pem-file-and-how-does-it-differ-from-other-openssl-generated-key-file). Just note that Let's Encrypt will create your certificates as PEM files.

Here are the one's that you will need, depending on the server software you are using (such as NGINX or Apache).

- _cert.pem_ - The server certificate that gets sent to the client, such as a web browser.
- _privkey.pem_ - The secret key that should be secured on your server.
- _chain.pem_ - intermediates
- _fullchain.pem_ - server cert + intermediates


## Done!
You now have certificates for your domain and can move on to setting up your server. In the future I hope to link instructions for different server environments below.

## Let's Encrypt 90 Day Expiry
As mentioned above, the certificate will expire after 90 days. [There is some rationale behind this](https://community.letsencrypt.org/t/maximum-and-minimum-certificate-lifetimes/264/2):
>At launch all certificates will have a lifetime of exactly 90 days. Post launch we will possibly offer more options, but they will likely be on the shorter side rather than the longer side. Part of the rationale for the 90 day number is that when certs are renewed only once a year, a lot can change. The person in charge might forget how to do it, or leave the organization, or change email addresses, etc. A shorter lifetime will hopefully encourage people to automate the renewal process, and we'll provide tools to help with that.

That means that unless you want to repeat this process every 90 days, you'll need to automate this. There will be follow up instructions on this at some point in the future.
