# CarTube

A standalone, **publicly-hostable** YouTube front-end the Tesla browser can open over the **car's own
internet connection** — no phone, no hotspot, no CarHey server. It's a single self-contained
`index.html` that uses YouTube's **official embed player** + the **YouTube Data API v3** for search.

> It does **not** rip/extract video streams (that violates YouTube's ToS). It embeds the official
> player, so it plays wherever the Tesla browser allows HTML5 video — reliably when parked.
> A webpage cannot override the car's own in-motion video lock, and this one doesn't try to.

---

## 1. Get a free YouTube Data API key (~2 min)

1. Open <https://console.cloud.google.com/> and create (or pick) a project.
2. Enable the API: <https://console.cloud.google.com/apis/library/youtube.googleapis.com> → **Enable**.
3. Credentials → **Create credentials → API key**. Copy the `AIza…` key.
4. **Restrict it** (recommended): click the key →
   - *API restrictions* → restrict to **YouTube Data API v3**.
   - *Application restrictions* → **HTTP referrers** → add your site's URL (e.g. `https://your-site.netlify.app/*`).
5. You enter this key **once** in CarTube's ⚙ settings (stored in the browser's localStorage — it is
   **not** baked into the public page source, so each device uses its own key).

Free quota is 10,000 units/day. A search costs 100 units, trending/detail calls cost ~1–3, so a
day of casual use is fine.

## 2. Host it (pick one — all give a free HTTPS URL)

HTTPS matters: it's needed for the embed + a secure context, and a public host gives it automatically.

- **Netlify Drop (zero account, fastest):** go to <https://app.netlify.com/drop> and drag this
  `cartube` folder onto the page. You instantly get a public `https://<name>.netlify.app` URL.
- **Cloudflare Pages / Vercel:** "deploy from folder" → upload the folder.
- **GitHub Pages:** push this folder to a repo, Settings → Pages → deploy from branch (`/root`).
  URL becomes `https://<user>.github.io/<repo>/`.

## 3. Use it in the car

1. In the Tesla browser, open your public URL.
2. Tap ⚙ (top right), paste your API key, Save.
3. Search (with live suggestions), scroll for more, or tap a category chip; tap a video to play.
   Tap **Auf YouTube öffnen** if a specific video disallows embedding.

## 4. (Optional) Sign in for Abos / Mag ich / Playlists

This adds personalised rows from **your** account: latest from your **subscriptions**, your **liked**
videos, and your **playlists**. (YouTube does **not** expose the algorithmic "Recommended for you" home
feed via any official API — no third-party app can show it. This is the closest legitimate version.)

Create an **OAuth Client ID** (separate from the API key):

1. Google Cloud → APIs & Services → **OAuth consent screen**: User type *External*, fill app name +
   your email, add scope `.../auth/youtube.readonly`, and add your own Google account under **Test users**
   (the app stays in "Testing" — that's fine for personal use).
2. → **Credentials → Create credentials → OAuth client ID** → type **Web application**.
   - **Authorized JavaScript origins:** `https://bta-private.github.io`
   - **Authorized redirect URIs:** `https://bta-private.github.io/cartube/`  *(exact, with trailing slash)*
3. Copy the Client ID (`…apps.googleusercontent.com`) and paste it into CarTube ⚙ → save.
4. Tap the 👤 icon (top right) → sign in. New chips **Abos / Mag ich / Playlists** appear.

Login uses the OAuth *implicit* flow (a redirect, no Google JS SDK) so it works on the old Tesla browser.
The token lasts ~1 hour and lives only in the tab's sessionStorage; after that, tap 👤 again.

---

## Notes / limits

- **Old Tesla Chromium (~v79):** the code avoids `?.`/`??`, flex `gap`, `aspect-ratio`/`inset`, etc.
- **TLS:** very old Tesla software occasionally fails to validate some modern certs. Netlify / GitHub
  Pages / Cloudflare generally work; if a host won't load, try another.
- **The in-motion block is the car's, not the page's.** Intended use is stationary/parked viewing.
