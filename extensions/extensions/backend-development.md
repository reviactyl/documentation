---
description: Injecting Routes, Controllers, and Hooks.
---

# Backend Development

The backend is PHP, running inside the panel's Laravel application. Your extension can add routes, Artisan commands, scheduled jobs and code that runs at boot.

Everything here is declared in the `backend` section of `extension.json`:

```json
"backend": {
    "routes": {
        "client": {
            "file": "backend/routes/client.php",
            "middleware": []
        }
    },
    "commands": [],
    "schedules": [],
    "providers": [],
    "boot_hooks": []
}
```

### Routes

Routes are ordinary Laravel route files. The example's whole `backend/routes/client.php` is:

```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/ping', fn () => ['extension' => 'ok']);
```

To register it, point to it from the manifest under `backend.routes`:

```json
"routes": {
    "client": {
        "file": "backend/routes/client.php",
        "middleware": []
    }
}
```

| Field        | Meaning                                                                                                             |
| ------------ | ------------------------------------------------------------------------------------------------------------------- |
| `file`       | Path to the route file, relative to the extension root.                                                             |
| `middleware` | List of middleware applied to every route in the file. Empty means none beyond the panel's defaults for that group. |

#### Route groups

The suggested layout has four route files, one per kind of route:

| File                        | Intended for                                                 |
| --------------------------- | ------------------------------------------------------------ |
| `backend/routes/client.php` | Endpoints for logged-in users (the client side of the panel) |
| `backend/routes/admin.php`  | Admin-only endpoints                                         |
| `backend/routes/api.php`    | API endpoints                                                |
| `backend/routes/web.php`    | Ordinary web routes                                          |

The example only declares `client`. The sources don't show manifest entries for `admin`, `api` or `web`, but the layout implies they're declared the same way: another key under `backend.routes` with its own `file` and `middleware`. Try it with the `client` entry as your template, then confirm it behaves as expected.

#### Finding your URL

The sources don't say what URL prefix the panel puts in front of extension routes, and we'd rather not guess. Ask Laravel directly:

```bash
php artisan route:list | grep ping
```

That shows the real path your `/ping` route was mounted at, and it's the quickest way to check a route registered at all.

### Commands, schedules, providers and boot hooks

The manifest has slots for the rest of the backend toolkit, matching what an extension can add (see [Getting Started](getting-started.md#what-you-need)):

| Key          | Adds                                                                                   |
| ------------ | -------------------------------------------------------------------------------------- |
| `commands`   | Artisan commands                                                                       |
| `schedules`  | Scheduler jobs                                                                         |
| `providers`  | Service providers                                                                      |
| `boot_hooks` | PHP that runs when the panel boots (for example, a file like `backend/hooks/boot.php`) |

The example extension leaves all four as empty arrays, and the sources don't include a worked example or the exact entry format for any of them. Rather than invent syntax, here's the honest guidance:

1. Start from the empty arrays the scaffold gives you.
2. Check the panel's source and the official docs for the entry format when you need one.
3. If you work it out, consider contributing an example back to the [example extension](https://github.com/reviactyl-community/example-extension).

### A word on the `.rext` package

The packaged example (`example-extension.rext`) contains a small `backend/Extension.php` file that isn't in the example's source directory. We don't know what generates it or what the panel does with it, so treat it as an implementation detail of packaging. See [Packaging Extension](packaging-extension.md).

### Testing a backend change

1. Link your extension with `php artisan d:extensions:dev my-extension --enable`.
2. Confirm it's enabled with `php artisan extensions:info my-extension`.
3. Check the route exists with `php artisan route:list`.
4. Call it, using a logged-in session for `client` routes.

If the route doesn't show up, go back to the [checklist](getting-started.md#checklist-before-you-blame-the-system), starting with whether `extension.json` is valid and the extension is enabled.
