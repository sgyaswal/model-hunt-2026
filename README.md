# MEGA Model Hunt 2026

Casting-call landing page for the MEGA International Model Hunt 2026 Mumbai
auditions. One static `index.html` — no build step, no dependencies — deployed
to GitHub Pages by GitHub Actions on every push to `main`.

**Live:** https://sgyaswal.github.io/model-hunt-2026/

## How an application reaches your inbox

GitHub Pages only serves static files, so it cannot send mail itself. The form
POSTs to [Web3Forms](https://web3forms.com), which relays each submission to the
inbox the access key is registered to.

```
visitor submits the form
        │  multipart POST (fields + the two photos)
        ▼
  api.web3forms.com
        │
        ▼
   your inbox ✉
```

The key is never committed. `index.html` ships with the placeholder
`__WEB3FORMS_KEY__`, and `.github/workflows/deploy.yml` swaps in the
`WEB3FORMS_KEY` repository secret while building the Pages artifact.

### One-time setup

1. Go to https://web3forms.com and enter **sgyaswal1@gmail.com**. The access key
   is emailed to you — that address is where applications will land, so it must
   be the one you want to receive them.
2. Add it to this repo:
   ```sh
   gh secret set WEB3FORMS_KEY --repo sgyaswal/model-hunt-2026
   ```
3. Re-run the deploy so the key is baked into the live page:
   ```sh
   gh workflow run "Deploy to GitHub Pages" --repo sgyaswal/model-hunt-2026
   ```

Until the secret exists the page still works: the submit button opens the
visitor's own email client, pre-filled with their answers, addressed to
`casting@megainternational.com`. The deploy logs a warning in that state rather
than failing.

## Local preview

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000. The mailto fallback is what runs locally — the
key is only injected during a deploy.

## Things worth knowing

- **Hero images are placeholders.** `img/hero-*.jpg` are generated gradients
  standing in for real photography. Drop in real 640/960/1240px-wide images
  under the same names. If you also produce `.avif` and `.webp` versions, re-add
  the two `<source>` lines noted in the comment inside `<picture>` — they were
  removed because a `<picture>` does not fall back when a source 404s.
- **Photo attachments** ride along with the POST. If they do not arrive, the
  Web3Forms plan on that key does not include file uploads; the text fields
  still come through either way.
- **The form collects no email address** — only a phone number and an optional
  Instagram handle. Add an email field if you want to reply by mail.
- **Spam** is filtered by a hidden `website` honeypot field, dropped silently
  in the client before the request is made.
