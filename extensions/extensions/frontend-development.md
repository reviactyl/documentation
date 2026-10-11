---
description: Inject UI Elements into your Panel
---

# Frontend Development

Your extension's frontend is React written in TypeScript. You can add content in two ways: **slots**, which inject a component into an existing page, and **routes**, which add entirely new pages.

All of this is declared in the `frontend` section of `extension.json`.

### Slots: adding content to existing pages

A slot is a named spot in the panel's UI where your component can appear. The example extension uses two:

```json
"slots": [
    {
        "name": "dashboard:router:above",
        "module": "frontend/src/dashboard.tsx",
        "export": "default",
        "order": 10
    },
    {
        "name": "dashboard:router:below",
        "module": "frontend/src/dashboardx.tsx",
        "export": "default",
        "order": 10
    }
]
```

| Field    | Meaning                                                                                                                                      |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`   | Which slot to inject into. `dashboard:router:above` renders above the dashboard router's content, `dashboard:router:below` renders below it. |
| `module` | Path to the file containing your component, relative to the extension root.                                                                  |
| `export` | Which export of that file to render. Use `default` for a default export.                                                                     |
| `order`  | Sort order when several extensions fill the same slot.                                                                                       |

These two are the only slot names we've seen in the sources. Others may exist, so check the panel's docs or source for the current list.

The component itself is plain React. This is the whole of the example widget:

```tsx
export default function DashboardExtensionWidget() {
    return (
        <div className='rounded-ui border border-gray-600 bg-gray-700 p-4 mt-4'>
            <h2 className='text-lg font-bold'>Hello Rext!</h2>
            <p className='text-sm text-gray-300'>Injected at dashboard:router:above</p>
        </div>
    );
}
```

The styling uses the panel's own Tailwind-style classes (`rounded-ui`, `bg-gray-700`, `text-gray-300`) so your widget matches the surrounding UI.

#### Available Slots

| Slot                              | Description                                             |
| --------------------------------- | ------------------------------------------------------- |
| `auth:form:above`                 | Above the login form                                    |
| `auth:card:before`                | Inside the authentication card, before the card content |
| `auth:form:after`                 | Immediately after the login form content                |
| `auth:form:below`                 | Below the login form                                    |
| `dashboard:above`                 | Above the main dashboard content                        |
| `dashboard:below`                 | Below the main dashboard content                        |
| `dashboard:router:above`          | Above the dashboard router content                      |
| `dashboard:router:below`          | Below the dashboard router content                      |
| `account:overview:above`          | Above the account overview section                      |
| `account:overview:column1:start`  | Start of column 1 in the account overview               |
| `account:overview:column1:middle` | Middle of column 1 in the account overview              |
| `account:overview:column1:end`    | End of column 1 in the account overview                 |
| `account:overview:column2:start`  | Start of column 2 in the account overview               |
| `account:overview:column2:middle` | Middle of column 2 in the account overview              |
| `account:overview:column2:end`    | End of column 2 in the account overview                 |
| `account:overview:below`          | Below the account overview section                      |
| `server:router:above`             | Above the server page router content                    |
| `server:router:below`             | Below the server page router content                    |
| `server:console:above`            | Above the server console                                |
| `server:console:below`            | Below the server console                                |
| `server:files:above`              | Above the server file manager                           |
| `server:files:actions:start`      | Start of the file manager actions toolbar               |
| `server:files:actions:end`        | End of the file manager actions toolbar                 |
| `server:files:below`              | Below the server file manager                           |
| `server:files:dropdown:start`     | Start of a file context/dropdown menu                   |
| `server:files:dropdown:end`       | End of a file context/dropdown menu                     |
| `server:backups:above`            | Above the server backups list                           |
| `server:backups:below`            | Below the server backups list                           |
| `server:backups:menu:start`       | Start of a backup context menu                          |
| `server:backups:menu:end`         | End of a backup context menu                            |
| `server:databases:above`          | Above the server databases list                         |
| `server:databases:below`          | Below the server databases list                         |
| `server:databases:menu:start`     | Start of a database row context menu                    |
| `server:databases:menu:end`       | End of a database row context menu                      |

### Routes: adding new pages

Routes come in two flavours, matching the two areas of the panel.

#### Dashboard pages

Under `frontend.routes.dashboardRouter`. These are account-level pages.

```json
"dashboardRouter": [
    {
        "path": "/account/my-extension",
        "label": "My Extension Route",
        "module": "frontend/src/dashboard-route.tsx",
        "export": "default",
        "icon": "icon:RiArchiveBox"
    }
]
```

The page component can use the panel's own UI building blocks. The example pulls in `Card`:

```tsx
import Card from '@/reviactyl/ui/Card'

export default function DashboardRouteExample() {
    return (
        <Card>
            <h2 className='text-xl font-bold'>Dashboard Route Example</h2>
            <p className='text-sm text-gray-300 mt-2'>
                This page is injected at /account/my-extension.
            </p>
        </Card>
    );
}
```

#### Server pages

Under `frontend.routes.serverRouter`. These appear within a server's own area, and can be limited by permission, egg or nest.

```json
"serverRouter": [
    {
        "path": "my-page",
        "label": "My Page",
        "module": "frontend/src/server-route.tsx",
        "export": "default",
        "permission": "activity.read",
        "eggIds": [1, 2, 3],
        "icon": "icon:RiBanknotes"
    }
]
```

#### Route fields

| Field        | Applies to | Meaning                                                                                                                                                                                                              |
| ------------ | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `path`       | both       | Where the page lives. Dashboard routes use a full path (`/account/my-extension`); server routes use a short name (`my-page`).                                                                                        |
| `label`      | both       | Text shown in navigation.                                                                                                                                                                                            |
| `module`     | both       | File containing the component.                                                                                                                                                                                       |
| `export`     | both       | Export name, usually `default`.                                                                                                                                                                                      |
| `icon`       | both       | <p>Navigation icon, written as <code>icon:</code> followed by an icon name such as <code>RiArchiveBox</code>.<br><br>All usable icons available at <a href="https://reviactyl.app/icons">reviactyl.app/icons</a></p> |
| `permission` | server     | Only show the page to users holding this permission, e.g. `activity.read`.                                                                                                                                           |
| `eggId`      | server     | Only show for servers using this one egg.                                                                                                                                                                            |
| `eggIds`     | server     | Only show for servers using any of these eggs.                                                                                                                                                                       |
| `nestId`     | server     | Only show for servers in this nest.                                                                                                                                                                                  |
| `nestIds`    | server     | Only show for servers in any of these nests.                                                                                                                                                                         |

The example extension includes one server route for each filter, so it's a good reference for how they behave. A route with none of the filters shows for every server.

### Building your frontend

You write source in `frontend/src` and the panel loads compiled JavaScript from `frontend/dist`. Compile once:

```bash
php artisan d:extensions:watch my-extension --once
```

Or keep it watching while you work:

```bash
php artisan d:extensions:watch my-extension
```

Combined with the dev link from [Getting Started](getting-started.md#see-it-running), the loop is: edit a file, let the watcher rebuild, refresh the panel.

#### Source vs compiled files

The manifest in the example points at `.tsx` source files (`frontend/src/dashboard.tsx`) and sets `"build_strategy": "source"`, with a `source` block listing the entry files to compile:

```json
"source": {
    "entry": "frontend/src/dashboard.tsx",
    "entries": [
        "frontend/src/dashboard-route.tsx",
        "frontend/src/server-route.tsx"
    ]
}
```

The official checklist, however, says module paths should point to JavaScript files in `frontend/dist/*.js` for runtime loading. Our reading is that with the `source` build strategy the panel compiles your listed entries into `dist` and loads the result, but the sources don't spell this out. If a page doesn't load, check both that the `dist` file exists and which path the manifest references.

#### How your code reaches the panel

You don't bundle React or the panel's UI components yourself. Looking at a compiled file from the example makes this clear:

```js
const React = window.React;
if (!React) throw new Error('window.React is not available for extension module rendering.');
const __extReviactylMod0 = window.__REVIACTYL_MODULES?.["reviactyl/ui/Card"];
if (!__extReviactylMod0) throw new Error("Missing Reviactyl runtime module: reviactyl/ui/Card");
const Card = __extReviactylMod0.default ?? __extReviactylMod0;
```

Your import of `@/reviactyl/ui/Card` becomes a lookup on the panel's `window.__REVIACTYL_MODULES`, and React comes from `window.React`. The practical consequences:

* Your extension shares the panel's copy of React, so there's no version clash.
* You can only import panel modules the panel actually exposes. Importing something it doesn't expose fails at runtime with a "Missing Reviactyl runtime module" error.
* Compiled files are small, since the heavy lifting lives in the panel.

### Troubleshooting

* **Nothing renders:** is the extension enabled, and does the file named in `module` exist?
* **"Missing Reviactyl runtime module":** you imported something the panel doesn't expose. Use only modules it provides.
* **Changes don't appear:** is the watcher running, and have you refreshed the page?
* **Page appears for the wrong servers:** re-check `eggId`, `eggIds`, `nestId`, `nestIds` and `permission`.
