# Migrating from Pterodactyl

## Compatibility

**Reviactyl** is backwards compatible with Pterodactyl. This means no data loss when switching between Pterodactyl & Reviactyl.

## Minimum PHP Requirements

PHP 8.2 or previous are no longer supported by Reviactyl v26. You need to upgrade php to minimum of 8.3 or higher (8.5 is recommended).

See [Upgrading PHP Here](../tutorial/upgrading-php.md).

You will need to have root access to your server in order to run and use this panel.

{% hint style="warning" %}
Make sure you have saved your `.env` file somewhere else in case of risk of losing it during migration.
{% endhint %}

{% stepper %}
{% step %}
## Clean-up Pterodactyl Old Files

```bash
cd /var/www/pterodactyl
rm -rf *
```
{% endstep %}

{% step %}
## Install PHP Intl Extension

Reviactyl v26 requires PHP 8.3 or newer and the `intl` extension. The Pterodactyl installation guide does not install `intl`, so add it:

```bash
apt install php8.3-intl
```

If `php -v` shows a version older than 8.3. Make sure to [upgrade your PHP First.](../tutorial/upgrading-php.md)


{% endstep %}

{% step %}
## Download & Install Latest Version of Reviactyl

```bash
cd /var/www/pterodactyl
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
chown -R www-data:www-data /var/www/pterodactyl/*

# If using NGINX on RHEL / Rocky Linux / AlmaLinux
chown -R nginx:nginx /var/www/pterodactyl/*

# If using Apache on RHEL / Rocky Linux / AlmaLinux
chown -R apache:apache /var/www/pterodactyl/*
```
{% endstep %}

{% step %}
## Restart Queue Worker

```bash
sudo systemctl restart pteroq.service
```
{% endstep %}
{% endstepper %}

That’s it! 🎉 Migration complete.
