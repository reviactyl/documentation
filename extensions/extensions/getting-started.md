---
description: Getting Started with Reviactyl Extensions
---

# Getting Started

This page is for someone who has never built a panel extension before. By the end you'll have an extension scaffolded, linked into a running panel, and showing up in the extension list.

### What is an extension?

An extension is a self-contained package that plugs extra behaviour into the panel. One extension can add any mix of:

* **Frontend routes**: whole new pages in the dashboard or on a server's sidebar
* **Frontend content**: small widgets injected into existing pages
* **Backend routes**: new HTTP endpoints
* **Artisan commands**: new `php artisan` commands
* **Scheduler jobs**: tasks that run on the panel's schedule
* **Boot-time PHP hooks**: code that runs when the panel starts

Every extension is identified by a unique **ID** and described by a file called `extension.json`. The ID is how you refer to it everywhere: on the command line, in file paths, and on disk.

### What you need

* A working Reviactyl panel you can run commands on. The docs assume the panel lives in `/var/www/reviactyl`.
* Shell access to that directory, since extensions are managed through `php artisan`.
* Basic comfort with PHP (for backend work) and React with TypeScript (for frontend work). You only need the half you plan to use.

A local or staging panel is strongly recommended. Don't develop against production.

### Create your first extension

From the panel directory, run:

```bash
cd /var/www/reviactyl
php artisan d:extensions:init
```

This creates the basic files for a new extension, starting with `extension.json`.

If you'd rather start from something that already does a bit of everything, copy the community example instead: [reviactyl-community/example-extension](https://github.com/reviactyl-community/example-extension). It contains a backend route, two dashboard widgets, a dashboard page and several server pages.

### See it running

While you're developing, you don't want to package and reinstall after every change. The dev command links your source folder into the panel instead:

```bash
php artisan d:extensions:dev my-extension --enable
```

Replace `my-extension` with your extension's ID. This sets up two links:

* `extensions/<id>` points at your source directory
* `public/extensions/<id>/frontend` points at your source `frontend` directory (if you have one)

Because these are links rather than copies, edits show up without reinstalling. When you're finished, remove the links:

```bash
php artisan d:extensions:dev my-extension --unlink
```

If your extension has frontend code, build it (see [Frontend Development](frontend-development.md)). The short version:

```bash
php artisan d:extensions:watch my-extension
```

### Check that the panel knows about it

These commands show what the panel thinks is going on:

```bash
php artisan extensions:list
php artisan extensions:info my-extension
php artisan extensions:enable my-extension
php artisan extensions:disable my-extension
```

An extension has to be **enabled** to do anything. If nothing seems to happen, start here.

### Checklist before you blame the system

Most "it doesn't work" moments come down to one of these:

* `extension.json` exists at the root of the extension.
* `api_version` includes `RCYL_v26`.
* Every frontend module referenced in the manifest actually exists.
* Module paths in the manifest point to JavaScript files (`frontend/dist/*.js`) for runtime loading. See the note in [Frontend Development](frontend-development.md).
* The extension is enabled.
* Nothing in the package contains path traversal (`../`).
