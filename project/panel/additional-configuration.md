# Additional Configuration

## OAuth

Reviactyl allows you to connect to OAuth providers such as Discord, Google, and GitHub.

### Discord

{% stepper %}
{% step %}
## Open the Discord Developer Portal

Open the [Discord Developer Portal](https://discord.com/developers/applications).
{% endstep %}

{% step %}
## Create an application

Click "New Application", name your application, and select "Create".
{% endstep %}

{% step %}
## Add the redirect URL

Navigate to the "OAuth2" tab and select "Add Redirect".

Enter the following URL as the redirect URL: `https://panel.example.com/auth/login/discord/callback`.
{% endstep %}

{% step %}
## Generate a client secret

Reset the client secret to generate a new one.
{% endstep %}

{% step %}
## Configure OAuth in Paymenter

Go to your Paymenter Admin Panel → Settings → OAuth.
{% endstep %}

{% step %}
## Add Discord credentials

Select "Discord" as the provider and enter the Client ID and Client Secret from the Discord Developer Portal.
{% endstep %}

{% step %}
## Save changes

Click "Save" to apply the changes.
{% endstep %}
{% endstepper %}

### Google

{% stepper %}
{% step %}
## Open Google Cloud Console

Open the [Google Cloud Console](https://console.cloud.google.com/).
{% endstep %}

{% step %}
## Select or create a project

Create a new project or select an existing one.
{% endstep %}

{% step %}
## Open credentials

Navigate to "APIs & Services" → "Credentials".
{% endstep %}

{% step %}
## Create OAuth credentials

Click "Create Credentials" and select "OAuth client ID".
{% endstep %}

{% step %}
## Configure the consent screen

Configure the consent screen and set the application type to "Web application".
{% endstep %}

{% step %}
## Add the redirect URI

Add the following redirect URI: `https://panel.example.com/auth/login/google/callback`.
{% endstep %}

{% step %}
## Save credentials

Save the changes and note down the Client ID and Client Secret.
{% endstep %}

{% step %}
## Configure OAuth in Paymenter

Go to your Paymenter Admin Panel → Settings → OAuth.
{% endstep %}

{% step %}
## Add Google credentials

Select "Google" as the provider and enter the Client ID and Client Secret from the Google Cloud Console.
{% endstep %}

{% step %}
## Save changes

Click "Save" to apply the changes.
{% endstep %}
{% endstepper %}

### GitHub

{% stepper %}
{% step %}
## Open GitHub Developer Settings

Open [GitHub Developer Settings](https://github.com/settings/profile).
{% endstep %}

{% step %}
## Create an OAuth application

Click "New OAuth App" and fill in the required details.
{% endstep %}

{% step %}
## Set the callback URL

Set the "Authorization callback URL" to `https://panel.example.com/auth/login/github/callback`.
{% endstep %}

{% step %}
## Save credentials

Save the changes and note down the Client ID and Client Secret.
{% endstep %}

{% step %}
## Configure OAuth in Paymenter

Go to your Paymenter Admin Panel → Settings → OAuth.
{% endstep %}

{% step %}
## Add GitHub credentials

Select "GitHub" as the provider and enter the Client ID and Client Secret from GitHub Developer Settings.
{% endstep %}

{% step %}
## Save changes

Click "Save" to apply the changes.
{% endstep %}
{% endstepper %}

## Backups

Reviactyl allows users to create backups of their servers. In order to create backups, a backup storage method has to be configured.

When changing Reviactyl's backup storage method, users may still download or delete existing backups from the prior storage driver. In the instance of migrating from S3 to local backups, S3 credentials must remain configured after switching to the local backup storage method.

### Using Local Backups

By default, Reviactyl uses local storage via Agent for backups. That said, this method of backup storage can be explicitly set with the following configuration in the `.env` file:

```dotenv
# Sets your panel to use local storage via Wings for backups
APP_BACKUP_DRIVER=agent
```

Do note that, when using local storage via Wings, the destination for backups is set in Wings' `config.yml` with the following setting key:

```yaml
system:
  backup_directory: /path/to/backup/storage
```

### Using S3 Backups

AWS S3 (or cloudflare R2 / any supported storage) can be used to store remote or cloud-based backups. The following configuration options have to be set in the `.env` file or as environment variables in order to enable it:

```dotenv
# Sets your panel to use s3 for backups
APP_BACKUP_DRIVER=s3

# Info to actually use s3
AWS_DEFAULT_REGION=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_BACKUPS_BUCKET=
AWS_ENDPOINT=
```

For some configurations, you might have to change your S3 URL from `bucket.domain.com` to `domain.com/bucket`. To accomplish this, add `AWS_USE_PATH_STYLE_ENDPOINT=true` to your `.env` file.

#### Multipart Upload

The S3 backup is using the S3 multipart upload capabilities. In rare situations, you might want to adjust the size of a single part or the lifespan of the generated pre-signed URLs. The default part size is 5GB, and the default pre-signed URL lifespan is 60 minutes.

You can configure the maximal part size using the `BACKUP_MAX_PART_SIZE` environment variable. You must specify the size in bytes. To define the pre-signed URL lifespan, use the `BACKUP_PRESIGNED_URL_LIFESPAN` variable. The expected unit is minutes.

The following `.env` snippet configures 1GB parts and uses 120 minutes as the pre-signed URL lifespan:

```dotenv
BACKUP_MAX_PART_SIZE=1073741824
BACKUP_PRESIGNED_URL_LIFESPAN=120
```

#### Storage Class

Should you need to specify a storage class, use the `AWS_BACKUPS_STORAGE_CLASS` environment variable. Default option is `STANDARD` (S3 Standard).

The following `.env` snippet sets the class to `STANDARD_IA` (this is an example).

```dotenv
# STANDARD_IA is an example.
AWS_BACKUPS_STORAGE_CLASS=STANDARD_IA
```

## Reverse Proxy Setup

When running Reviactyl behind a reverse proxy, such as [Cloudflare's Flexible SSL](https://support.cloudflare.com/hc/en-us/articles/200170416-What-do-the-SSL-options-mean-) or Nginx/Apache/Caddy, etc., you will need to make a quick modification to the Panel to ensure things continue to work as expected. By default, when using these reverse proxies, your Panel will not correctly handle requests. You'll most likely be unable to login or see security warnings in your browser console as it attempts to load insecure assets. This is because the internal logic the Panel uses to determine how links should be generated thinks it is running over HTTP and not over HTTPS.

You will need to edit the `.env` file in the Panel's root directory to contain `TRUSTED_PROXIES=*` at minimum. We highly suggest providing a specific IP address (or comma-separated list of IPs) rather than allowing `*.` For example, if your proxy is running on the same machine as the server, the chances are that something like `TRUSTED_PROXIES=127.0.0.1` will work for you.

### NGINX Specific Configuration

For Reviactyl to properly respond to an NGINX reverse proxy, the `NGINX` location config must contain the following lines:

```nginx
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header Host $host;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_redirect off;
proxy_buffering off;
proxy_request_buffering off;
```

### Cloudflare Specific Configuration

If you're using Cloudflare's Flexible SSL you should set `TRUSTED_PROXIES` to contain their [IP Address](https://www.cloudflare.com/ips/). Below is an example of how to set this.

```dotenv
TRUSTED_PROXIES=173.245.48.0/20,103.21.244.0/22,103.22.200.0/22,103.31.4.0/22,141.101.64.0/18,108.162.192.0/18,190.93.240.0/20,188.114.96.0/20,197.234.240.0/22,198.41.128.0/17,162.158.0.0/15,104.16.0.0/13,104.24.0.0/14,172.64.0.0/13,131.0.72.0/22
```

## reCAPTCHA

The Panel uses invisible reCAPTCHA to secure the login page from brute-force attacks. If the login attempt is considered suspicious, users may be required to perform a reCAPTCHA challenge.

### Configuring reCAPTCHA

While we provide a global Site Key and Secret Key by default, we highly recommend changing it for your own setup.

You can generate your own keys in the [reCAPTCHA Admin Console](https://www.google.com/recaptcha/admin)

The keys can then be applied using the Settings in the admin panel. The reCAPTCHA settings can be found on the **Advanced** tab.

### Disabling reCAPTCHA (via Admin Panel)

{% hint style="warning" %}
**Security Warning**

We do not recommend disabling reCAPTCHA. It is a security mechanism that makes it harder to perform brute-force attacks on user accounts.
{% endhint %}

If users have trouble logging in, or your Panel isn't exposed to the internet, it can make sense to disable reCAPTCHA.

reCAPTCHA can easily be disabled using the admin panel. In the **Settings**, select the **Advanced** tab and set the Status of reCAPTCHA to disabled.

### Disabling reCAPTCHA (via Database)

If you cannot access your panel, you can modify the database directly using the following commands.

```bash
# If using MariaDB (v11.0.0+)
mariadb -u root -p

# If using MySQL
mysql -u root -p
```

```sql
UPDATE panel.settings SET value = 'false' WHERE `key` = 'settings::recaptcha:enabled';
```

## 2FA

If possible you should use the panel to update your 2FA settings. If you can't access your panel for what ever reason you can use the following steps.

### Disable 2FA requirement

```bash
# If using MariaDB (v11.0.0+)
mariadb -u root -p

# If using MySQL
mysql -u root -p
```

```sql
UPDATE panel.settings SET value = 0 WHERE `key` = 'settings::pterodactyl:auth:2fa_required';
```

### Disable 2FA for a specific user

Run the following command in your `/var/www/reviactyl` directory.

```bash
php artisan p:user:disable2fa
```

## Telemetry

Since 2.2.x, the Panel collects anonymous metrics about the Panel and all connected nodes. This feature is enabled by default, but can be disabled.

The data collected by this feature is not sold or used for advertising purposes. Aggregate statistics may be made public or shared with third-parties for the purposes of improving the software.

### How does it work?

The Telemetry system works by first generating a random UUIDv4 identifier for the Panel installation. This identifier is stored in the database so people load-balancing multiple Panel instances can still have a unique identifier. This identifier is then sent to a remote server, along the associated telemetry data. The telemetry data is collected every 24 hours, there is no ongoing collection or local storage of the telemetry data, we collect the data right before we send it to the remote server.

Currently, all telemetry collection logic is handled by the [TelemetryCollectionService](https://github.com/reviactyl/panel/blob/develop/app/Services/Telemetry/TelemetryCollectionService.php) on the panel. This service is responsible for collecting all the data that is sent to the remote server.

### What data is collected?

If you wish to see the full data that is collected, please look at the TelemetryCollectionService (as linked above), or use the `php artisan p:telemetry` command to view the exact data that will be sent to the remote server.

**NOTE**: Versions before v26.10.0 (October release) no longer receives telemetry data as of 2026-09-28.

As of 2026-10-01, the data collected consists of:

* Unique identifier for the Panel
* Version of the Panel
* PHP version
* Backup storage driver (S3, Local, etc.)
* Cache driver (Redis, Memcached, etc.)
* Database driver and version (MySQL, MariaDB, PostgreSQL, etc.)
* Resources
  * Allocations
    * Total number
    * Total number of used allocations (assigned to a server)
  * Backups
    * Total number
    * Sum of the total amount of bytes stored by backups
  * Eggs
    * Total number
  * Locations
    * Total number
  * Mounts
    * Total number
  * Nests
    * Total number
  * Nodes
    * Total number
  * Servers
    * Total number
    * Number of servers that are suspended
  * Users
    * Total number
    * Number of users that are admins
* Nodes
  * Node UUID
  * Version of Wings on the node
  * Docker
    * Version
    * Cgroups
      * Driver
      * Version
    * Containers
      * Total
      * Running
      * Paused
      * Stopped
    * Storage
      * Driver
      * Filesystem
    * runc
      * Version
  * System
    * Architecture ( `amd64`, `arm64`, etc.)
    * CPU Threads
    * Memory Bytes
    * Kernel Version
    * Operating System (Debian, Fedora, RHEL, Ubuntu, etc.)
    * Operating System Type (bsd, linux, windows, etc.)

### How is the data stored?

Currently, the data is stored with Namecrane Hosting, we ingest all telemetry data with a PHP Worker which does basic processing such as validation and then inserts it into a private directory.

You can get the total unique UUIDS using `GET https://reviactyl.app/api/v26/get-installs` request.

### Enabling Telemetry

Telemetry is enabled by default, if you want to enable it after disabling it, edit your `.env` file and either remove the `PANEL_TELEMETRY_ENABLED` line, or set it to `true`.

```dotenv
PANEL_TELEMETRY_ENABLED=true
```

You may also use the `php artisan p:environment:setup` command to enable telemetry, optionally with the `--telemetry` flag for a non-interactive setup.

### Disabling Telemetry

To disable telemetry, edit your `.env` file and set `PANEL_TELEMETRY_ENABLED` to `false`.

```dotenv
PANEL_TELEMETRY_ENABLED=false
```

You may also use the `php artisan p:environment:setup` command to disable telemetry, optionally with the `--telemetry=false` flag for a non-interactive setup.
