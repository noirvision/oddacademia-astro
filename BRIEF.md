# Brief: Webflow → Astro migration — Odd Academia landing

## Context
Repo `noirvision/oddacademia-astro` (public). `source/` holds an unmodified Webflow
export: a one-page landing (`index.html`) plus `privacy-policy.html` and
`terms-and-conditions.html`. Originally built in Webflow for Hyper; this is a
portfolio rebuild. Deploy target: Cloudflare Pages (static), connected to the repo
after this task.

## Goal
Convert the export into an Astro project at the repo root with 1:1 visual and
behavioural parity with the original. Do not modify `source/` — it is the reference.

## Stack & structure
- Latest stable Astro, static output, npm, Node 22. No Tailwind, no UI frameworks.
- Keep Webflow CSS as is (normalize.css, components.css, odd-academia.css).
- `src/layouts/Base.astro`: head, meta, Open Graph, fonts, scripts.
- `src/components/`: one component per section of the landing (header, hero, …, footer).
- Pages: `index.astro`, `privacy-policy.astro`, `terms-and-conditions.astro`.
- Replace the Google WebFont loader with `<link>` + preconnect, same families/weights,
  `display=swap`.

## Images
- Content images → `src/assets/`, rendered with `astro:assets` `<Picture>`,
  formats AVIF + WebP with a fallback, responsive widths, explicit width/height,
  lazy loading below the fold, eager + `fetchpriority="high"` for the hero image.
- Drop Webflow's manual responsive variants (`-p-500`, `-p-800` …) — Astro generates its own.
- CSS background images: keep, but compress losslessly where it helps.
- `opengraph.png` stays PNG in `public/` (social previews), compressed.
- Fonts, favicon and other non-content files → `public/`.

## Interactions
- Keep `webflow.js` and IX2 interactions working for now: preserve `data-w-id`
  attributes and the `data-wf-page` / `data-wf-site` attributes on `<html>`.
  Rewriting interactions is a later iteration.

## Form
- Keep the Subscribe form markup visible, but no network request may be sent.
  On submit: preventDefault, show the Webflow success state (`.w-form-done`),
  hide the form. Make sure webflow.js's own form handler does not fire.
- Add a `TODO` comment where a webhook will be connected later.

## Links
- Internal links to the policy pages → clean routes (`/privacy-policy`,
  `/terms-and-conditions`); add redirects from the `.html` URLs in `public/_redirects`.
- Find every empty or `#` link. Fix what has an obvious target; list the rest in the report.

## SEO / indexing
- This is a portfolio copy: `noindex` everywhere — `public/_headers` with
  `X-Robots-Tag: noindex` for `/*` and `<meta name="robots" content="noindex">`.
- Keep original title, description and OG tags.
- Footer credit line: "Portfolio rebuild by noirvision — original built in Webflow for Hyper."

## Repo hygiene
- `.gitignore`: node_modules, dist, .astro, .DS_Store, *.zip.
- No secrets, webhook URLs, API keys or tokens in the repo, ever. Future ones go into
  Cloudflare Pages environment variables.
- `README.md`: what the project is, stack, how to run and build, deploy notes, and
  "Design and content © their respective owners. Rebuilt by noirvision for portfolio purposes."

## Verification
- `npm run build` must succeed with no errors or warnings you can fix.
- Serve `source/` and the Astro preview build side by side; take screenshots at
  1440px and 390px wide of every page and compare. Fix differences.
- Check that interactions run and the form sends no request.
- If Lighthouse is available, run it on both and report the scores.
- If the network blocks a tool you need (browser download, fonts, Lighthouse),
  say which host was blocked and continue with what is possible.
- Background jobs: when you start a dev/preview/static server or any other process in the
  background, record its PID and wait on it with an exit condition tied to the process
  dying (`wait` / PID check), not only to an expected string appearing in its output.
  Stop every server you started before finishing.

## Delivery
- Work on a branch and open a PR to `main`.
- Final report: what was done, component list, links fixed and links still empty,
  total image weight before and after, any deviations from the original,
  Lighthouse numbers if measured.