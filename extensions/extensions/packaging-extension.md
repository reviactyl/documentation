---
description: Package and Compile your Extension
---

# Packaging Extension

Once your extension works under the dev link, you package it into a single file others can install. That file has the extension `.rext`.

### Before you package

Run through this quickly:

* &#x20;`extension.json` is valid, with a sensible `id`, `version` and `api_version`.
* Your frontend is built (`php artisan d:extensions:watch my-extension --once`) so `frontend/dist` is up to date.
* Every `module` path in the manifest points at a file that exists.
* No path in the package contains `../`.
* You've removed the dev link, or at least you know it's there: `php artisan d:extensions:dev my-extension --unlink`.

### Build the package

```bash
php artisan d:extensions:compile
```

This packages your extension. The example extension's README passes the extension ID:

```bash
php artisan d:extensions:compile my-extension
```

Passing the ID is the safer habit if you have more than one extension in development. If your panel complains about the argument, drop it.

### What's inside a `.rext`

A `.rext` is a plain ZIP archive. You can open it with any unzip tool to check what you're shipping. The example's package contains:

```
backend/routes/client.php
backend/Extension.php
extension.json
frontend/src/dashboard.tsx
frontend/src/dashboardx.tsx
frontend/src/server-route.tsx
frontend/src/dashboard-route.tsx
frontend/dist/dashboard.js
frontend/dist/dashboardx.js
frontend/dist/server-route.js
frontend/dist/dashboard-route.js
```

Two things to notice:

* **`extension.json` sits at the root** of the archive, not inside a wrapper folder. If you ever zip an extension by hand, zip the _contents_ of the folder, not the folder itself.
* **Both `src` and `dist` are included.** The compiled JavaScript is what runs; the source travels with it.

List the contents of your own package to confirm:

```bash
unzip -l my-extension.rext
```

### Test the package like a user would

A package that works under the dev link can still be broken, for example by a missing file. Test the real install path on a clean panel.

The example's README describes the install flow. Copy the `.rext` into the panel root, then:

```bash
php artisan extension:install my-extension
php artisan extension:enable my-extension
```

(These are the singular spellings from the example README; the docs site's equivalents for the other commands are plural, so check `php artisan list | grep extension` if one isn't found.)

Then confirm:

1. `php artisan extensions:list` shows it, enabled.
2. Your pages appear and your slots render.
3. Your backend routes respond.
4. Disabling it (`php artisan extensions:disable my-extension`) removes your pages cleanly.

### Versioning

Bump `version` in `extension.json` every time you cut a new package. Use `MAJOR.MINOR.PATCH`, as the example's `0.1.0` suggests. A `0.x` version tells people the extension is still settling.
