---
description: Publish your Extension to our Marketplace
---

# Publishing Extension

You've got a working package. This page is about getting it to the people who'd use it.

There are two ways to publish a Reviactyl extension:

| Option                                           | Best for                                        | Cost to users                           |
| ------------------------------------------------ | ----------------------------------------------- | --------------------------------------- |
| [https://rextstore.app/](https://rextstore.app/) | Reaching panel owners through a dedicated store | You choose: sell it or post it for free |
| [https://github.com/](https://github.com/)       | Open-source extensions and community sharing    | Free                                    |

You can do both. Many authors keep the source on GitHub and list a packaged build on the store.

### Publishing on Rextstore

[Rextstore](https://rextstore.app/) is where extensions can be published for others to find and install. You can **sell** your extension or **post it for free**. The choice is yours.

#### What Rextstore requires

Your submission has to meet these criteria:

1. **Follow the official structure.** Your extension must be laid out the way the docs describe: `extension.json` at the root, with `backend/` and `frontend/` folders as needed.
2. **Be zipped, and include a `README.md`.** Submit your extension as a `.zip` archive. The `README.md` is your setup guide, so it needs to be in the package.
3. **Include a `LICENSE` (recommended).** Not mandatory, but strongly encouraged.

#### Pre-submission checklist

* Structure matches the official layout, with `extension.json` at the root of the archive.
* &#x20;`extension.json` is valid and `version` is bumped for this release.
* `api_version` and `target_version` match what you actually tested.
* Frontend is built, so `frontend/dist` is current.
* `README.md` is in the package and explains setup.
* `LICENSE` is included.
* Installed from the package on a clean panel and everything works.
* No secrets, credentials or `../` paths anywhere in the package.

#### Making the zip

Zip the _contents_ of your extension folder so `extension.json` sits at the top level of the archive, not inside a wrapper folder:

```bash
cd my-extension
zip -r ../my-extension-v1.1.1.zip . -x "*.git*"
```

Then check it:

```bash
unzip -l ../my-extension-v1.1.1.zip
```

You should see `extension.json`, `README.md` and `LICENSE` at the top level, with `backend/` and `frontend/` beside them.

A `.rext` file produced by `d:extensions:compile` is itself a ZIP archive (see Packaging Extension), but the store asks for a `.zip` that contains a README. If you're unsure whether to upload the compiled `.rext` or a zip of your folder, check the submission form on Rextstore, since that's where the exact expectations live.

#### Free or paid?

Rextstore lets you do either. Some things worth thinking through, none of which are store rules:

* **Free** works well for small utilities, examples and anything you want the community to build on.
* **Paid** makes sense for larger or well-supported extensions where you'll be maintaining it over time. If you charge, be ready to provide updates and answer setup questions.
* Say clearly in your README what the extension does, so buyers know what they're getting.

We haven't seen Rextstore's pricing, payout or review details in the sources behind these docs, so check the store itself for those.

### Publishing on GitHub

The second option is a public GitHub repository, which is how the community [example extension](https://github.com/reviactyl-community/example-extension) is shared.

A well-formed repo looks like this:

* The source lives under `extensions/<id>/`.
* A ready-built `.rext` sits in the repo root, so people don't have to build it.
* A `README.md` gives install and development steps.
* A `LICENSE` file, MIT in the example.
* `update_url` in `extension.json` points back at the repository.

GitHub is free, open and easy to contribute to, but it has no storefront, so you'll need to tell people where to find it. Tag a release that matches the `version` in `extension.json`, and attach the built package to the release.

### Writing a good README

Whichever route you choose, the README is the first thing a user reads. For Rextstore it's required, and it's your setup guide. Cover:

* **What it does,** in a sentence or two.
* **Requirements,** meaning which panel version it targets.
* **Install steps.** The example extension's README is a good template:

```markdown
# My Extension

- Copy `my-extension.rext` to your Reviactyl root directory.
- Run `php artisan extension:install my-extension`.
- Run `php artisan extension:enable my-extension`.
```

* **Configuration,** if there's anything to set up after install.
* **Development,** if you welcome contributions: how to link and watch the extension.
* **Data and external services,** if your extension stores data or calls out to anything.

### Choosing a licence

Rextstore recommends a `LICENSE` file, and it's a good idea regardless. Without one, others have no clear right to use, change or share your work. The example extension uses MIT, which is short and permissive. If you're selling your extension, pick terms that match how you want it used, and say so plainly in the file.

### After you publish

* **Bump `version` for every release,** and keep it in sync with your tags and uploads.
* **Re-test when the panel updates.** The extension API is still work in progress, so a panel update can change behaviour. Update `target_version` and `api_version` once you've verified a new release.
* **Keep `update_url` accurate.** It's what update checks use. It can be `null` if you don't want update checks.
* **Never ship secrets.** Anything in the package is readable by whoever installs it.
* **Support your users,** especially if they paid.
