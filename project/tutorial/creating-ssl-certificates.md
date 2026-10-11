# Creating SSL Certificates

This tutorial covers creating SSL certificates for your panel and Wings.

{% tabs %}
{% tab title="Method 1: Certbot" %}
Install Certbot and the plugin for your web server. These commands are for Ubuntu.

```bash
sudo apt update
sudo apt install -y certbot
# Run this if you use Nginx
sudo apt install -y python3-certbot-nginx
# Run this if you use Apache
sudo apt install -y python3-certbot-apache
```

### Create a certificate

Replace `example.com` with your domain. Add another `-d` flag for each additional domain.

Use the web-server plugin where possible. For Wings-only machines, use the standalone or DNS method.

If your web server already uses SSL, Certbot applies the certificate without a restart. Follow the [web server configuration](../panel/webserver-configuration.md) first.

#### HTTP challenge

The HTTP challenge requires port `80` to be accessible.

```bash
# Nginx
certbot certonly --nginx -d example.com
# Apache
certbot certonly --apache -d example.com
# Standalone — stop your web server first
certbot certonly --standalone -d example.com
```

#### DNS challenge

The DNS challenge verifies ownership with a TXT record. It does not require port `80`.

```bash
certbot -d example.com --manual --preferred-challenges dns certonly
```

### Auto-renewal

Open root's crontab with `sudo crontab -e`. This job checks for renewal every day at 23:00.

Change `nginx` to `apache` or `wings` when required.

```
0 23 * * * certbot renew --quiet --deploy-hook "systemctl restart nginx"
```

### Troubleshooting

An `Insecure Connection` or SSL/TLS error can indicate an expired certificate. If renewal fails because port `80` is in use, use the Nginx or Apache plugin. Alternatively, stop the relevant service, renew the certificate, then start it again.

{% stepper %}
{% step %}
#### Stop Nginx

Replace `nginx` with your web server or `wings` where appropriate.

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

```bash
systemctl start nginx
```
{% endstep %}

{% step %}
#### Restart Wings

Some services require a restart before they use the renewed certificate.

```bash
systemctl restart wings
```
{% endstep %}
{% endstepper %}
{% endtab %}

{% tab title="Method 2: acme.sh (Cloudflare API)" %}
Use this method when the server cannot access port `80`. These commands use Ubuntu and Cloudflare DNS.

```bash
curl https://get.acme.sh | sh
```

### Create the certificate directory

The panel configuration expects Certbot-compatible paths.

```bash
sudo mkdir -p /etc/letsencrypt/live/example.com
```

### Configure Cloudflare credentials

Create a Cloudflare API token that can edit DNS records in the target zone. Export the token before issuing the certificate:

```bash
export CF_Token="your_cloudflare_api_token"
export CF_Account_ID="your_cloudflare_account_id"
export CF_Zone_ID="your_cloudflare_zone_id"
```

For a legacy Global API Key, use:

```bash
export CF_Key="your_cloudflare_api_key"
export CF_Email="your_cloudflare_email"
```

### Issue the certificate

Replace `example.com` with your domain.

```bash
acme.sh --issue --dns dns_cf -d "example.com" --server letsencrypt \
  --key-file /etc/letsencrypt/live/example.com/privkey.pem \
  --fullchain-file /etc/letsencrypt/live/example.com/fullchain.pem
```

### Auto-renewal

acme.sh creates a renewal job after its first run. Review or adjust its schedule with:

```bash
sudo crontab -e
```
{% endtab %}

{% tab title="Method 3: Caddy (Cloudflare API)" %}
Use this method when Cloudflare proxy mode is enabled or port `80` is unavailable.

### Install Caddy with the Cloudflare DNS plugin

Caddy does not include the Cloudflare DNS plugin by default. Choose one installation method:

1. Build Caddy with [`xcaddy`](https://caddyserver.com/docs/build#xcaddy).
2. Download a custom build from [Caddy's download page](https://caddyserver.com/download).
3. Use [caddy-ansible](https://github.com/caddy-ansible/caddy-ansible).

### Create a Cloudflare API token

Use Cloudflare's **Edit zone DNS** template. Restrict **Zone Resources** to the target DNS zone. Ensure an `A` or `CNAME` record points to the target server.

### Store the token securely

Create `/etc/caddy/.secrets.env`:

```bash
CLOUDFLARE_API_TOKEN=<your_cloudflare_api_token>
```

Restrict the file to the `caddy` user:

```bash
chown caddy:caddy /etc/caddy/.secrets.env
chmod 0600 /etc/caddy/.secrets.env
```

### Load the environment file

Update the Caddy systemd service, usually at `/etc/systemd/system/caddy.service`. Add `--envfile /etc/caddy/.secrets.env` to `ExecStart`.

```ini
[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target

[Service]
Type=notify
User=caddy
Group=caddy
ExecStart=/usr/bin/caddy run --environ --envfile /etc/caddy/.secrets.env --config /etc/caddy/Caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

### Configure Caddy

Add a `tls` block under your domain in `/etc/caddy/Caddyfile`:

```nginx
example.com {
  # ...

  tls {
    dns cloudflare {env.CLOUDFLARE_API_TOKEN}
  }
}
```
{% endtab %}
{% endtabs %}
