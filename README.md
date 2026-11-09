# SD Studio (in-browser)

A private Stable Diffusion website where the model runs **inside the visitor's browser**
(SD-Turbo via ONNX Runtime Web + WebGPU). No server, no API key, no cost per image.

- **Text → Image** and **Image → Image**, 512×512, about 1 step per image.
- The model (~2.5 GB, from Hugging Face `schmuell/sd-turbo-ort-web`) downloads once and is cached by the browser.
- Needs **WebGPU**: a recent Chrome or Edge on a computer with a decent graphics chip (or recent Chrome on Android).
  Older phones and most iPhones may not have enough memory.

## Private and hidden from search engines

- Password screen (the SHA-256 of the password is in `index.html`; change it with
  `printf %s 'new password' | sha256sum` and replace `PASSWORD_SHA256`).
  It keeps casual visitors out; it is not strong security, because the page's code is public.
- `robots.txt` blocks all crawlers, and every page carries `noindex, nofollow` meta tags.

## Hosting on Firebase

Any static host works. This repo deploys it to **Firebase Hosting** automatically with GitHub Actions
(`.github/workflows/firebase-hosting.yml`). One-time setup:

1. **Create a Firebase project** at <https://console.firebase.google.com> (Add project; Google Analytics can be off).
   Note its **Project ID** (e.g. `sd-studio-1234`).
2. **Create a deploy key**: Google Cloud console → IAM & Admin → Service Accounts
   (<https://console.cloud.google.com/iam-admin/serviceaccounts>, pick the same project) → Create service account →
   give it the roles **Firebase Hosting Admin** and **API Keys Viewer** → open it → Keys → Add key → JSON. A `.json` file downloads.
3. **Give it to GitHub** (never paste it anywhere else): repo → Settings → Secrets and variables → Actions →
   - **Secrets** tab → New repository secret → name `FIREBASE_SERVICE_ACCOUNT`, value = the whole content of the `.json` file.
   - **Variables** tab → New repository variable → name `FIREBASE_PROJECT_ID`, value = your Project ID.
4. **Deploy**: repo → Actions → "Deploy SD Studio to Firebase Hosting" → Run workflow
   (it also runs on every change to `sd-studio-web/`).

The site goes live at `https://<project-id>.web.app`.

Deploying from your own computer instead: `npm i -g firebase-tools`, `firebase login`, then
`cd sd-studio-web && firebase deploy --only hosting --project <project-id>`.
