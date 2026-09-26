# Odd Academia — Astro rebuild

A portfolio rebuild of the **Odd Academia** landing page. The original was built
in Webflow for [Hyper](https://www.hyperhq.com/). This repo converts the Webflow
export into a static [Astro](https://astro.build) site with the same look and
behaviour.

- `source/` is the untouched Webflow export, kept as the visual/behavioural reference. Don't edit it.
- Everything else is the Astro project.

## Stack

- Astro 7 (static output), npm, Node 24 (LTS)
- The original Webflow CSS (`normalize.css`, `components.css`, `odd-academia.css`), unmodified
- `webflow.js` + jQuery 3.5.1 (self-hosted) for the Webflow IX2 interactions and slider
- jQuery Nice Select for the "I am a" dropdown
- `astro:assets` `<Picture>` for content images (AVIF + WebP, PNG/JPG fallback, responsive widths)
- No Tailwind, no UI framework

## Structure

```
public/            static files served as-is (_headers, _redirects, opengraph.png, icons, JS)
src/
  assets/          content images, optimised at build time by <Picture>
  images/          CSS background images (referenced from odd-academia.css)
  styles/          Webflow CSS + astro.css (small Astro-specific additions)
  layouts/Base.astro
  components/      one component per landing section
  pages/           index, privacy-policy, terms-and-conditions
source/            original Webflow export (reference only)
```

## Run and build

```sh
npm install
npm run dev        # http://localhost:4321
npm run build      # static site in dist/
npm run preview    # serve dist/ locally
```

## Deploy (Cloudflare Pages)

- Framework preset: Astro. Build command `npm run build`, output directory `dist`.
- Set `NODE_VERSION=24` in the Pages environment variables (also in `.nvmrc`).
- `public/_headers` sends `X-Robots-Tag: noindex` for every URL (this is a portfolio copy);
  every page also has `<meta name="robots" content="noindex">`.
- `public/_redirects` maps the old Webflow URLs (`/privacy-policy.html`, `/terms-and-conditions.html`)
  to the clean routes.
- `site` in `astro.config.mjs` is used for absolute Open Graph URLs. Update it if the
  project gets a different `*.pages.dev` name or a custom domain.

### Secrets

Never commit secrets, webhook URLs, API keys or tokens. The waitlist form currently
sends nothing (see the `TODO` in `src/components/Waitlist.astro`); when a webhook is
connected, its URL goes into a Cloudflare Pages environment variable (read server-side,
e.g. by a Pages Function), never into client code.

## Credits

Design and content © their respective owners. Rebuilt by noirvision for portfolio purposes.
