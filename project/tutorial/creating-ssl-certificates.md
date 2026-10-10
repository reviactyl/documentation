# Creating SSL Certificates

This tutorial briefly covers creating new SSL certificates for your panel and wings.

## Method 1: Certbot

To begin, we will install certbot, a simple script that automatically renews our certificates and allows much easier creation of them. The command below is for Ubuntu distributions, but you can always check [Certbot's official site](https://certbot.eff.org/) for installation instructions. We have also included a command below to install certbot's Nginx/Apache plugin so you won't have to stop your webserver.

```bash
sudo apt update
sudo apt install -y certbot
# Run this if you use Nginx
sudo apt install -y python3-certbot-nginx
# Run this if you use Apache
sudo apt install -y python3-certbot-apache
```

## Creating a Certificate

After installing the certbot, we need to generate a certificate. There are a couple of ways to do that, but the easiest is to use the web server-specific certbot plugin you just installed. For Wings-only machines that don't need a web server, use the standalone or DNS method of the certbot as you don't need a web server for it.

Then, in the command below, you should replace `example.com` with the domain you would like to generate a certificate for. When you have multiple domains you would like certificates for, simply add more `-d anotherdomain.com` flags to the command. You can also look into generating a wildcard certificate but that is not covered in this tutorial.

When you are using certbot's Nginx/Apache plugin, you won't need to restart your webserver to have the certificate applied assuming that you've already configured the webservers to use SSL as instructed in the [web server configuration step](../panel/webserver-configuration.md).

### HTTP challenge

HTTP challenge requires you to expose port 80 for the challenge verification.

```bash
# Nginx
certbot certonly --nginx -d example.com
# Apache
certbot certonly --apache -d example.com
# Standalone - Use this if neither works. Make sure to stop your webserver first when using this method.
certbot certonly --standalone -d example.com
```

### DNS challenge

DNS challenge requires you to create a new TXT DNS record to verify domain ownership, instead of having to expose port 80. The instructions are displayed when you run the certbot command below.

```bash
certbot -d example.com --manual --preferred-challenges dns certonly
```

### Auto Renewal

You'll also probably want to configure the automatic renewal of certificates to prevent unexpected certificate expirations. You can open crontab with `sudo crontab -e` and add the line from below to the bottom of it for attempting renewal every day at 23 (11 PM).

Deploy hook would restart the Nginx service to apply a new certificate when it's renewed successfully. Change `nginx` in the restart command to suit your own needs, such as to `apache` or `wings`.

For advanced users, we suggest installing and using [acme.sh](https://acme.sh/) which provides more options, and is much more powerful than certbot.

```bash
0 23 * * * certbot renew --quiet --deploy-hook "systemctl restart nginx"
```

### Troubleshooting

If you get an `Insecure Connection` or SSL/TLS related error when trying to access your panel or wings, the certificate has likely expired. This can be easily fixed by renewing the SSL certificate, although using the command `certbot renew` might not do the job if port 80 is in use, as it'll return errors like: `Error: Attempting to renew cert (domain) from /etc/letsencrypt/renew/domain.conf produced an unexpected error`.

This will happen especially if you're running Nginx instead of Apache. The solution for this is to use Nginx or Apache plugins with `--nginx` and `--apache`. Alternatively, you can stop Nginx, then renew the certificate, and finally restart Nginx. Replace `nginx` with your own web server or with `wings` should you be renewing the certificate for Wings.

{% stepper %}
{% step %}
#### Stop Nginx

```bash
systemctl stop nginx
```
{% endstep %}

{% step %}
#### Renew the certificate

```bash
certbot renew
```
{% endstep %}

{% step %}
#### Restart Nginx

Once the process has completed, you can restart the Nginx service:

```bash
systemctl start nginx
```
{% endstep %}

{% step %}
#### Restart Wings

You may also need to restart Wings as not every service is able to automatically apply an updated certificate:

```bash
systemctl restart wings
```
{% endstep %}
{% endstepper %}
