# Upgrading from v2

## Minimum PHP Requirements

PHP 8.2 or previous are no longer supported by Reviactyl v26. You need to upgrade php to minimum of 8.3 or higher (8.5 is recommended). Alternatively you can use [Reviactyl v2](https://v2.reviactyl.app/)

You will need to have root access to your server in order to run and use this panel.

{% stepper %}
{% step %}
## Clean-up Reviactyl Old Files

{% hint style="warning" %}
**Pre-caution!**

Make sure you have saved your `.env` file somewhere else in case of risk of losing it during migration.
{% endhint %}

```bash
cd /var/www/reviactyl
rm -rf *
```
{% endstep %}

{% step %}
## Install PHP Intl Extension

Reviactyl v26 requires PHP 8.3 or newer and the `intl` extension. The 2.x installation guide does not install `intl`, so add it:

```bash
apt install php8.3-intl
```

If `php -v` shows a version older than 8.3. Make sure to upgrade your PHP First.\
<br>
{% endstep %}

{% step %}
## Download & Install Latest Version of Reviactyl

```bash
cd /var/www/reviactyl
curl -Lo panel.tar.gz https://github.com/reviactyl/panel/releases/latest/download/panel.tar.gz
tar -xzvf panel.tar.gz
chmod -R 755 storage/* bootstrap/cache/
```
{% endstep %}

{% step %}
## Download Composer Dependencies

```bash
COMPOSER_ALLOW_SUPERUSER=1 composer install --no-dev --optimize-autoloader
```
{% endstep %}

{% step %}
## Run Database Migrations

```bash
php artisan migrate --seed --force
```
{% endstep %}

{% step %}
## Set Permissions

```bash
# If using NGINX, Apache or Caddy (not on RHEL / Rocky Linux / AlmaLinux)
chown -R www-data:www-data /var/www/reviactyl/*

# If using NGINX on RHEL / Rocky Linux / AlmaLinux
chown -R nginx:nginx /var/www/reviactyl/*

# If using Apache on RHEL / Rocky Linux / AlmaLinux
chown -R apache:apache /var/www/reviactyl/*
```
{% endstep %}

{% step %}
## Restart Queue Worker

```bash
sudo systemctl restart reviq.service
```
{% endstep %}
{% endstepper %}

That’s it! 🎉 Migration complete.
