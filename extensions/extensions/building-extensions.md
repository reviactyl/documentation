---
description: Building Reviactyl Extensions
---

# Building Extensions

This page covers how an extension is laid out on disk and what goes in `extension.json`, the file that ties everything together.

### Extensions Structure

A practical extension looks like this:

```
my-extension/
├── extension.json
├── backend/
│   ├── routes/
│   │   ├── client.php
│   │   ├── admin.php
│   │   ├── api.php
│   │   └── web.php
│   └── hooks/
│       └── boot.php
├── frontend/
│   ├── src/
│   │   ├── index.tsx
│   │   └── pages/
│   │       └── DashboardPage.tsx
│   └── dist/
│       └── (compiled)
├── public/
│   └── images/
├── private/
├── data/
└── cache/
```

You don't need all of it. The only hard requirement is `extension.json` at the root. Here's what each part is for:

| Path                          | Purpose                                                                |
| ----------------------------- | ---------------------------------------------------------------------- |
| `extension.json`              | The manifest. Declares your ID, version, routes, slots and more.       |
| `backend/routes/`             | PHP route files. One file per kind of route (client, admin, API, web). |
| `backend/hooks/boot.php`      | PHP that runs at boot time.                                            |
| `frontend/src/`               | Your TypeScript/React source.                                          |
| `frontend/dist/`              | Compiled JavaScript, generated from `src`. Don't edit by hand.         |
| `public/`                     | Static files such as images.                                           |
| `private/`, `data/`, `cache/` | Space for files your extension keeps for itself.                       |

The example extension is deliberately small. It only has `backend/routes/client.php`, `frontend/src`, `frontend/dist` and the manifest.

### The manifest: `extension.json`

Start with the fields every extension needs:

```json
{
    "id": "my-extension",
    "name": "My Extension",
    "version": "0.1.0",
    "api_version": "RCYL_v26"
}
```

#### Core fields

| Field            | Required | Description                                                                                    |
| ---------------- | -------- | ---------------------------------------------------------------------------------------------- |
| `id`             | Yes      | Identifier for the extension. Must be a slug: letters, numbers, `_` and `-`.                   |
| `name`           | Yes      | Human-readable name.                                                                           |
| `version`        | Yes      | Current version of your extension, e.g. `0.1.0`.                                               |
| `api_version`    | Yes      | The extension API version you built against. Must be a supported value (currently `RCYL_v26`). |
| `description`    | No       | A short line about what it does.                                                               |
| `author`         | No       | Your name or organisation.                                                                     |
| `website`        | No       | Link to your site or documentation.                                                            |
| `update_url`     | No       | URL used to check for updates. Can be `null`.                                                  |
| `target_version` | No       | The panel version you're building for. The example uses `canary`.                              |

Pick your `id` carefully. It's used in file paths and commands, and changing it later makes your extension look like a different one.

#### Section fields

Beyond the core fields, the example manifest uses these top-level sections:

| Section         | What goes in it                                           | Covered in                                      |
| --------------- | --------------------------------------------------------- | ----------------------------------------------- |
| `backend`       | Routes, commands, schedules, providers, boot hooks        | [Backend Development](backend-development.md)   |
| `frontend`      | Build strategy, slots, routes, source entry points        | [Frontend Development](frontend-development.md) |
| `assets`        | Extra assets (empty in the example)                       | n/a                                             |
| `permissions`   | Permissions the extension declares (empty in the example) | n/a                                             |
| `feature_flags` | Feature flags (empty in the example)                      | n/a                                             |

We haven't seen a worked example of `assets`, `permissions` or `feature_flags` in use, so we don't describe their contents here. They're present in the example as empty arrays and are safe to leave that way.

### A complete example

This is the manifest from the example extension, trimmed to the interesting parts:

```json
{
    "id": "example-extension",
    "name": "Example Extension",
    "version": "0.1.0",
    "description": "An Example Extension.",
    "author": "Reviactyl",
    "website": "https://reviactyl.app/",
    "update_url": "https://github.com/reviactyl/example-extension/",
    "api_version": "RCYL_v26",
    "target_version": "canary",
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
    },
    "frontend": {
        "build_strategy": "source",
        "entry_points": [],
        "slots": [
            {
                "name": "dashboard:router:above",
                "module": "frontend/src/dashboard.tsx",
                "export": "default",
                "order": 10
            }
        ],
        "routes": {
            "dashboardRouter": [
                {
                    "path": "/account/my-extension",
                    "label": "My Extension Route",
                    "module": "frontend/src/dashboard-route.tsx",
                    "export": "default",
                    "icon": "icon:RiArchiveBox"
                }
            ],
            "serverRouter": []
        },
        "source": {
            "entry": "frontend/src/dashboard.tsx",
            "entries": [
                "frontend/src/dashboard-route.tsx",
                "frontend/src/server-route.tsx"
            ]
        }
    },
    "assets": [],
    "permissions": [],
    "feature_flags": []
}
```

Read it top to bottom: identity first, then what the backend adds, then what the frontend adds. That's the shape every manifest follows.

### Good habits

* **Start small.** Get a single route or widget working before adding more.
* **Keep paths relative to the extension root.** `backend/routes/client.php`, never an absolute path.
* **Never use `../` in paths.** The panel rejects package content containing path traversal.
* **Bump `version` whenever you release.** Update checks and your users rely on it.
