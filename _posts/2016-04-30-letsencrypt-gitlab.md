---
layout: post

title: Hands on Let's Encrypt
subtitle: "Let’s Encrypt is a new Certificate Authority: It’s free, automated, and open."
clean_excerpt: "Let’s Encrypt is a new Certificate Authority: It’s free, automated, and open. And finally I've been able to use it for an internal GitLab server that we used over http. Now we are running over https!"

author:
  name: Gerard Solé
  link: http://gsole.cat
  twitter: GerardSoleCa
  bio: Software Engineer
  image: gs.jpg

---
Let’s Encrypt is a new Certificate Authority: It’s free, automated, and open. And finally I've been able to use it for an internal GitLab server that we used over http. Now we are running over https!

![Let's Encrypt](https://letsencrypt.org/images/letsencrypt-logo-horizontal.svg)

This entry is a *tl;dr* of [https://webnugget.de/setting-up-gitlab-with-free-ssl-certs-from-lets-encrypt-on-ubuntu-14-04/](https://webnugget.de/setting-up-gitlab-with-free-ssl-certs-from-lets-encrypt-on-ubuntu-14-04/) which explains from the very beginning how to implement the full software stack. Here I’m omitting some parts and some descriptions. I'm also using this post entry as a preservation of the original content. So full merits for them.
<!-- more -->

## Install Let's Encrypt
As we want to use Let's Encrypt to generate valid server certificates first of all the required software will be installed. Remember to elevate your permissions to admin rights.
{% highlight bash %}
sudo su
{% endhighlight %}
Then, install git and clone the project repository.
{% highlight bash %}
apt-get update
apt-get install git
cd /root
git clone https://github.com/letsencrypt/letsencrypt  
{% endhighlight %}

## Configure Let's Encrypt
This will provide the necessary configuration to resolve Let's Encrypt certificates.

{% highlight bash %}
mkdir /root/letsencrypt-config
nano /root/letsencrypt-config/gitlab.ini
{% endhighlight %}

Paste the following lines. Remember to change the *domains* and *email* lines. Email will be used by let's encrypt just in case something happen with your certificate.

{% highlight bash %}
# this is the let's Encrypt config for our gitlab instance

# use the webroot authenticator.
 authenticator = webroot
# the following path needs to be served by our webserver
# to validate our domains
 webroot-path = /var/www/letsencrypt

# generate certificates for the specified domains.
domains = gitlab.yourdomain.com

# register certs with the following email address
email = your@email.com

# use a 4096 bit RSA key instead of 2048
rsa-key-size = 4096  
{% endhighlight %}

Then, create the directory which will serve the authentication files.

{% highlight bash %}
mkdir -p /var/www/letsencrypt
{% endhighlight %}

## Configure GitLab

Edit GitLab's configuration:

{% highlight bash %}
nano /etc/gitlab/gitlab.rb
{% endhighlight %}

Remember to change *gitlab.yourdomain.com* to your server Hostname. Keep in mind that some of the following configurations could already be on your GitLab instance. **Proceed with caution**.

{% highlight bash %}
external_url "http://gitlab.yourdomain.com/"

nginx['redirect_http_to_https'] = true

nginx['ssl_certificate']= "/etc/letsencrypt/live/gitlab.yourdomain.com/fullchain.pem"

nginx['ssl_certificate_key'] = "/etc/letsencrypt/live/gitlab.yourdomain.com/privkey.pem"

nginx['custom_gitlab_server_config']="location ^~ /.well-known {\n alias /var/www/letsencrypt/.well-known;\n}\n"  
{% endhighlight %}

Apply the new configuration.

{% highlight bash %}
gitlab-ctl reconfigure
{% endhighlight %}

## Getting the certificates

To get the certificates, run the following command:

{% highlight bash %}
/root/letsencrypt/letsencrypt-auto certonly -c /root/letsencrypt-config/gitlab.ini
{% endhighlight %}

Once the certificates are resolved, change again GitLab's configuration.

{% highlight bash %}
nano /etc/gitlab/gitlab.rb
{% endhighlight %}

*change the external_url to use https instead of http*

After that, apply again the configuration.

{% highlight bash %}
gitlab-ctl reconfigure
{% endhighlight %}

Now, you should be able to access to your GitLab instance by https instead of http.

# Autoupdate for certificates

Let's Encrypt certificates have a valid life of 90 days, so to it's important to keep them up to date. A cron job will be used to renew the certificate once per month.

{% highlight bash %}
nano /etc/cron.monthly/renew-ssl-certificates
{% endhighlight %}

Paste the following configuration:

{% highlight bash %}
#!/bin/bash

/root/.local/share/letsencrypt/bin/letsencrypt certonly -c /root/letsencrypt-config/gitlab.ini --renew-by-default

gitlab-ctl restart
{% endhighlight %}

Finally give execution permisions to the file.

{% highlight bash %}
chmod +x /etc/cron.monthly/renew-ssl-certificates
{% endhighlight %}
