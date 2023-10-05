---
layout: post
title:  "Running FastAPI with TLS/HTTPS on an EC2 instance"
category: software
---

Setting up [FastAPI](https://fastapi.tiangolo.com/) with TLS/HTTPS on an Amazon [EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) instance.

I spent about 8 hours figuring this out. Now that I've been through it once I bet I could set it up again in 30-40 minutes.

# Getting Amazon EC2 up and running

* [Set up to use Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html). I wasn't able to select the Security Group I created when creating an EC2 instance. I tried modifying the default Security Group to allow SSH but that didn't seem to work. I didn't end up using a Security Group, instead I checked the boxes on the EC2 instance creation page for allowing http, https, and ssh. (Wasted an hour figuring that out.)
* [Tutorial: Get started with Amazon EC2 Linux instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html).
* [Install LAMP on Amazon Linux 2023](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-lamp-amazon-linux-2023.html). Installed LAMP to get "Hello World" (actually it says "IT WORKS!") for http.

# Getting HTTPS/TLS to work

* I created a cheap domain name through [Hover](hover.com): asdfasdf.fun. Beware of the "sale price", it will renew at the non-sale price. This one was $5 for the first year and ~$30/year after that. I didn't see any domains that renew for less than $20/year so I'm going to roll with this one, but I turned off auto-renew. (After all this is just an API experiment.) After setting up a CNAME for www and linking it to the ec2 instance public URL I was able to navigate to www.asdfasdf.fun after a few mins.
* FYI worth skimming, but I ended up using the certbot apache plugin approach instead in the next step: [Configure SSL/TLS on Amazon Linux 2023](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2023.html)
* Amazon Linux 2023 is [based on Fedora](https://aws.amazon.com/linux/amazon-linux-2023/faqs/), following the [certbot instructions for Fedora](https://certbot.eff.org/instructions?ws=apache&os=fedora), snapd wasn't available with dnf, so followed Rich Brome's answer to [StackExchange: Installing Let's Encrypt on Amazon Linux 2023](https://unix.stackexchange.com/a/754933/67183)
* Running /opt/certbot/bin/certbot --apache gave an error about not finding a virtual host on port 80. The "Short Answer for CentOS" in Adam Winter's answer to [Unable to find a virtual host listening on port 80.... Please add a virtual host for port 80](https://stackoverflow.com/a/62080140/79878) fixed it for me. Up and running on https!
* Lots more to know if you plan to do this for real... since I plan to let this domain name expire in a year I didn't bother with auto-renewal. The [Test and harden the security configuration](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2023.html#ssl_test_2023) recommends services that test your security.

# Install Nginx and configure TLS

* Disable apache (in fact you never had to do that in the first place, I was just using it as a bootstrapping step. Nginx by default puts a web page up at port 80.)

    $ sudo systemctl stop httpd

* Install pip and nginx:

    $ sudo dnf install python3-pip nginx

* FYI the [certbot user guide](https://eff-certbot.readthedocs.io/en/stable/using.html#where-are-my-certificates) was helpful for finding where the certificates were located and which ones to use for configuring nginx.
* In /etc/nginx/nginx.conf I commented out the port 80 server and uncommented the port 443 server. Then updated these fields:
```
    server {
        listen       443 ssl http2;
        listen       [::]:443 ssl http2;
        server_name  www.asdfasdf.fun;     # UPDATED
        root         /usr/share/nginx/html;
        location / {                              # ADDED
                proxy_pass http://localhost:8000; # ADDED
        }                                         # ADDED
        ssl_certificate "/etc/letsencrypt/live/www.asdfasdf.fun/fullchain.pem"; # UPDATED
        ssl_certificate_key "/etc/letsencrypt/live/www.asdfasdf.fun/privkey.pem"; # UPDATED
        ...
    }
```
* Start nginx and enable it to start at boot:

    $ sudo systemctl start nginx.service

    $ sudo systemctl enable nginx.service

# Install and run FastAPI

* From [Fast API Installation](https://fastapi.tiangolo.com/#installation):
... added the main.py hello world FastAPI file from the [FastAPI installation page](https://fastapi.tiangolo.com/#installation) to the ec2-user directory and ran the FastAPI server (default is port 8000):

    $ cd

    (create main.py file)

    $ uvicorn main:app --reload

* Note the default port is 8000 which matches the nginx.conf file above.
* Navigating to https://www.asdfasdf.fun shows { "Hello World" }. Works!

# Notes

* I was past the free period on my account so I've been paying $0.29 / day for the default tc2.micro instance. It looks like tc3.micro might be cheaper?
* Stopping/starting the instance gives it a new IP address and DNS name, so you need to update that with your Domain Name provider. It took only a couple of minutes after updating to be able to ssh into the instance, but still a pain. To have a permanent IP address you can get an [Elastic IP address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html), which seems to be free as long as the service it's connected to is running.
