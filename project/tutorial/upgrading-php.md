# Upgrading PHP

Move the Panel to a newer version of PHP.

## Introduction

Reviactyl v26 requires PHP 8.3 and above (8.5 recommended). If you run an older version, such as PHP 8.1 or 8.2 from Pterodactyl and Reviactyl 2.x, upgrade PHP before you upgrade the Panel.

| Panel Version | PHP Versions  |
| ------------- | ------------- |
| 26.x          | 8.3, 8.4, 8.5 |
| 1.x to 2.x    | 8.2, 8.3      |

This guide upgrades Ubuntu or Debian to PHP 8.5. For PHP 8.3, replace `8.5` with `8.3` in every command.

## Installing the New Version

Add the `ondrej/php` repository, which provides newer PHP versions for Ubuntu:

```bash
apt -y install software-properties-common
LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php
```

On Debian, skip this step. Sury's repository, added in [Getting Started](https://docs.pterodactyl.io/v2/panel/getting-started#installing-dependencies), already provides newer PHP versions.

Install the new version with the extensions the Panel needs:

```bash
apt update
apt -y install php8.5 php8.5-{common,cli,gd,mysql,mbstring,bcmath,xml,fpm,curl,zip,intl}
```

The new version becomes the default `php` command. Check it with:

```bash
php -v
```

## Updating the Web Server

The web server still sends requests to the old version's PHP-FPM, so point it at the new one.

{% tabs %}
{% tab title="NGINX" %}
Change the PHP-FPM socket in your Panel's configuration, then reload NGINX:

```bash
sed -i -e 's/php8\.[0-9]-fpm\.sock/php8.5-fpm.sock/' /etc/nginx/sites-available/pterodactyl.conf
systemctl reload nginx
```
{% endtab %}

{% tab title="Apache" %}

{% endtab %}

{% tab title="Caddy" %}

{% endtab %}
{% endtabs %}

## Restarting the Queue Worker

The queue worker runs the old version until you restart it:

```bash
systemctl restart reviq
```

## Checking the Panel

To confirm the new version has everything the Panel needs, run:

```bash
cd /var/www/reviactyl
composer check-platform-reqs --no-dev
```

Then open the Panel in your browser. Once everything works, you can remove the old version, for example with `apt -y purge 'php8.3*'`.
