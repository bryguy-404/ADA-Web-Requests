# Alpha Dog Agency — Website Edit Requests

A focused, static Astro page for existing clients. Formstack owns the form fields, validation, file uploads, and submissions. The project has one direct dependency: Astro.

## Local development

Use Node.js **22.22.2** (pinned in `.nvmrc`; Astro requires Node 22.12 or newer).

```sh
npm ci
npm run dev
```

The development URL is `http://localhost:4321`.

```sh
npm run build
npm run preview
```

`npm run build` generates the static site in `dist/`. Preview serves that production output locally. An internet connection is required to load the live Formstack form; the page's logo, fonts, styles, and icons are local assets.

## GitHub and Cloudflare Pages

Create an empty GitHub repository, then run these commands from this directory (replace the remote URL with yours):

```sh
git init -b main
git add .
git commit -m "Build Alpha Dog client website request page"
git remote add origin https://github.com/YOUR-ACCOUNT/alpha-dog-web-request.git
git push -u origin main
```

In Cloudflare, open **Workers & Pages → Create application → Pages → Import an existing Git repository**, then select the repository. Use:

| Setting | Value |
| --- | --- |
| Framework preset | Astro |
| Production branch | `main` |
| Root directory | Repository root (leave blank) |
| Build command | `npm run build` |
| Build output directory | `dist` |
| Node version | `22.22.2` (from `.nvmrc`, or set `NODE_VERSION` explicitly) |

No application environment variables, Cloudflare adapter, Worker, Functions, database, authentication, or secrets are required. Commit `package-lock.json`; do not commit `node_modules/` or `dist/`.

Cloudflare provides a `*.pages.dev` URL after deployment and preview URLs for branch/PR deployments. The site uses root-relative asset paths and does not hardcode a deployment hostname.

Later, add `requests.alphadogagency.com` in the Pages project's **Custom domains** section and follow Cloudflare's DNS instructions. The application does not need a code change for that subdomain.

Reference: [Cloudflare's Astro Pages guide](https://developers.cloudflare.com/pages/framework-guides/deploy-an-astro-site/).

## Formstack integration

The supplied script and `<noscript>` link live in `src/pages/index.astro`. Astro's `is:inline` directive preserves the external script as a classic script at its position in the document. This is required because the current Formstack embed uses `document.write`.

- Keep the script free of `async`, `defer`, and `type="module"`.
- Keep the normal full-page load; do not add Astro's client router around this embed.
- Do not enable Cloudflare Rocket Loader for this page; it can change script execution timing.
- Form edits are made in Formstack and appear automatically on the next page load.
- The small CSS adjustments only widen the native form, expose keyboard focus, and keep mobile inputs at a readable size. No field definitions or submission behavior are replaced.
- A direct hosted-form link is always available if scripts are blocked or the embed fails. The supplied `<noscript>` fallback is also preserved.
- If adding a Content Security Policy later, test all Formstack scripts, styles, fonts, uploads, and submission endpoints before enforcing it. A restrictive policy can prevent the third-party form from working.

Reference: [Astro external script handling](https://docs.astro.build/en/guides/client-side-scripts/#load-external-scripts).

## Branding and maintenance

`src/styles/global.css` contains the page styling. The primary colors match [Alpha Dog Agency's current website](https://www.alphadogagency.com/): deep green `#1b3b1f`, heading green `#122915`, and lime `#bcd631`. Satoshi regular and medium fonts are self-hosted in `public/fonts/` with `font-display: swap`.

Brand assets were sourced from the existing Alpha Dog site:

- Logo: [original image](https://framerusercontent.com/images/mHLOp7tjVcTNEHnjdWEnvOiruXU.png)
- Favicon and touch icon: [original brand icon](https://framerusercontent.com/images/ETTJNZWetRIWFKWuRXr7tLfDrU.png)
- Satoshi: the regular and medium WOFF2 files served by the reference site's Fontshare integration; [Satoshi font information](https://www.fontshare.com/fonts/satoshi)

Keep these assets with this Alpha Dog project. The copyright year is generated at build time.

The page deliberately includes `noindex, nofollow` metadata and a matching Cloudflare `_headers` rule because it is a client utility page. These discourage search indexing; they do not restrict access. To make the page indexable later, remove both rules. `_headers` is applied by Cloudflare Pages, not Astro's local preview server.

## Release check

1. Run `npm ci` and `npm run build`.
2. Open `npm run preview`; confirm the actual Formstack form loads once.
3. Check desktop and mobile widths, keyboard navigation, and the direct-form link.
4. After deploying, check the `pages.dev` URL. If you need to test receipt of a real submission or a file upload, coordinate that test with the agency so it does not become an unintended client request.
