# Updating the Panel

This documentation covers the process for updating within the `v26.x.x` series of releases. This means updating from — for example — `v26.05.0` to `v26.05.01`.

## Automatic Upgrade

Automatic Upgrades are available starting from `v26.10.0`. Guide will be available soon after `.01`

## Manual Upgrade

{% stepper %}
{% step %}
### Delete Old Files

```bash
cd /var/www/reviactyl
find . -mindepth 1 -maxdepth 1 ! -name 'public' ! -name '.env' ! -name 'extensions' ! -name 'storage' ! -name 'database' -exec rm -rf -- {} +
```

Recommended to run inorder to fix conflicts.

{% hint style="warning" %}
**RUN THIS COMMAND ON YOUR REVIACTYL ROOT DIRECTORY (Example - `/var/www/reviactyl/`)**
{% endhint %}
{% endstep %}

{% step %}
### Download & Install Latest Version of Reviactyl

```bash
cd /var/www/reviactyl
curl -Lo panel.tar.gz https://github.com/reviactyl/panel/releases/latest/download/panel.tar.gz
tar -xzvf panel.tar.gz
chmod -R 755 storage/* bootstrap/cache/
```
{% endstep %}

{% step %}
### Download Composer Dependencies

```bash
COMPOSER_ALLOW_SUPERUSER=1 composer install --no-dev --optimize-autoloader
```
{% endstep %}

{% step %}
### Run Database Migrations

```bash
php artisan migrate --seed --force
```
{% endstep %}

{% step %}
### Set Permissions

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
### Restart Queue Worker

```bash
sudo systemctl restart reviq.service
```
{% endstep %}
{% endstepper %}
