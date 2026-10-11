---
description: Steps to Build Reviactyl Panel
---

# Building Panel

{% hint style="warning" %}
Do **not** run the following steps on your production server.
{% endhint %}

The frontend of the Panel is built with React. Any changes to the source files require to recompile it. This also applies to style sheets. The following sections explain how to do so.

Instructions are also available in [README.md ](https://github.com/reviactyl/panel/blob/develop/BUILDING.md)

### Install Dependencies

The following commands will install the necessary dependencies for building the Panel assets.

The build tools require NodeJS, pnpm is used as the package manager.

```bash
# Ubuntu/Debian
curl -sL https://deb.nodesource.com/setup_22.x | sudo -E bash - 
sudo apt install -y nodejs

# CentOS
curl -sL https://rpm.nodesource.com/setup_22.x | sudo -E bash -
sudo yum install -y nodejs yarn # CentOS 7
sudo dnf install -y nodejs yarn # CentOS 8, Rocky Linux 8, AlmaLinux 8
```

Install pnpm and required packages

```bash
npx get-pnpm # Installs pnpm

cd /var/www/reviactyl
pnpm install # Installs panel frontend dependencies
```

### Build Panel Assets

The following command will rebuild the Panel frontend.

```bash
cd /var/www/reviactyl
pnpm build # Build panel
```

You can use command `pnpm dev` to view the progress of your changes in almost real-time for easier development. Once you're satisfied with your changes build the panel using the previously mentioned `pnpm build` command.
