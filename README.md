# Rina & Taufiq — Wedding Invitation Site

A mobile-first digital wedding invitation with RSVP, a live wishes wall, countdown, and Google Maps/Calendar/Sheets integration.

```
wedding-site/
├── index.html              # the entire site — one file, edit CONFIG at the bottom of the <script>
├── apps-script/Code.gs      # paste into Google Apps Script — this is your RSVP backend
├── assets/
│   ├── images/               # put bride/groom photos here
│   ├── video/                 # pre-wedding video, if used
│   └── audio/                  # background music (mp3)
└── README.md
```

No build tools, no dependencies, no server required — it's a static site.

---

## 1. Replace the wedding details

Everything editable lives in **one place**: the `CONFIG` object near the bottom of `index.html`'s `<script>` tag.

```js
const CONFIG = {
  brideName: "Rina Kustiyawati",
  groomName: "Mohamad Taufiq Ardy Pradana",
  weddingDateISO: "2026-11-29T13:00:00+07:00",
  venueAddress: "Ds Pelang Rt. 005 / Rw. 003, Mayong, Jepara, Jawa Tengah",
  venueMapsQuery: "Ds Pelang Mayong Jepara",
  gift: { bank: "Bank ABC", account: "1234 5678 9012", holder: "a.n. Rina Kustiyawati" },
  GOOGLE_SCRIPT_URL: "", // set this in step 3
  ...
};
```

Change names, date, venue, and gift/bank info here — the countdown, calendar link, maps link, and all on-page text update automatically. `weddingDateISO` **must** keep the `+07:00` (WIB) offset.

Guests opening a link like `yoursite.com/?to=Budi+Santoso` will see their name on the cover and pre-filled in the RSVP form — handy if you send personalized links.

---

## 2. Add your media

The provided folder wasn't attached to this conversation, so the site currently ships with clearly-labeled placeholder photo circles and no audio file. To add your own:

- **Photos**: drop files into `assets/images/`, then in `index.html` replace the placeholder `<div class="photo" id="bride-photo">…</div>` with `<div class="photo" id="bride-photo" style="background-image:url('assets/images/bride.jpg')"></div>` (same for groom). Compress photos to <300KB each (e.g. squoosh.app) so the site stays fast on mobile data.
- **Video**: place in `assets/video/`, and add an `<video>` element wherever you'd like it (e.g. in the Bride & Groom section) — keep it under ~10MB and add `preload="none"` so it doesn't block page load.
- **Music**: place an MP3 at `assets/audio/wedding-song.mp3` (the `<audio>` tag already points here). Most mobile browsers block autoplay, which is why the site only starts music on the "Buka Undangan" tap — that's expected and by design, not a bug.

---

## 3. Connect Google Sheets (RSVP database)

1. Create a new Google Sheet — name it whatever you like (e.g. "Wedding RSVP — Rina & Taufiq").
2. In the Sheet, go to **Extensions → Apps Script**.
3. Delete the default `myFunction` code and paste in the full contents of `apps-script/Code.gs`.
4. Click **Deploy → New deployment**.
5. Click the gear icon next to "Select type" → choose **Web app**.
6. Set:
   - **Execute as**: Me
   - **Who has access**: Anyone
7. Click **Deploy**. Google will ask you to authorize the script — this is expected (it's your own script accessing your own sheet). Approve it.
8. Copy the **Web app URL** it gives you (ends in `/exec`).
9. Paste that URL into `CONFIG.GOOGLE_SCRIPT_URL` in `index.html`.

That's it — every RSVP submission now appends a row to your sheet in real time (columns: Timestamp, Nama, Kehadiran, Jumlah Tamu, Ucapan & Doa), and the "Wedding Wishes" section polls the same script every 15 seconds to show new messages.

**Managing responses**: just open the Google Sheet — sort, filter, or use **File → Download → CSV/Excel** to export any time. No extra plugin needed.

**If you ever edit `Code.gs`**: after changing the script, go to **Deploy → Manage deployments → edit (pencil) → New version → Deploy**, so the live URL picks up your changes.

---

## 4. Deploy to GitHub Pages

I don't have access to your GitHub account, so this last step needs to happen on your end — it only takes a few minutes:

1. Create a new repository on GitHub (e.g. `rina-taufiq-wedding`), public or private (Pages works with either on a paid plan; public repos get Pages free).
2. Upload this whole `wedding-site` folder's contents to the repo root (drag-and-drop on github.com works, or via git):
   ```bash
   git init
   git add .
   git commit -m "Wedding invitation site"
   git branch -M main
   git remote add origin https://github.com/<your-username>/rina-taufiq-wedding.git
   git push -u origin main
   ```
3. On GitHub, go to the repo's **Settings → Pages**.
4. Under "Build and deployment", set **Source: Deploy from a branch**, **Branch: main**, folder **/ (root)**. Save.
5. GitHub will publish it at `https://<your-username>.github.io/rina-taufiq-wedding/` within a minute or two.
6. (Optional) Add a custom domain under the same Pages settings if you own one.

---

## 5. Maintenance checklist

- **Change the date/venue later** → edit `CONFIG` in `index.html`, commit, push. GitHub Pages redeploys automatically within a minute.
- **View/manage RSVPs** → open the Google Sheet directly.
- **Guest list is getting long / want to close RSVPs** → you can hide the RSVP form by adding `style="display:none"` to `<section class="rsvp" ...>`, wishes wall keeps working.
- **Broken Apps Script URL** → re-check step 3; the URL must end in `/exec`, and "Who has access" must stay "Anyone".
- **Site looks unstyled** → check that the Google Fonts `<link>` tags in `<head>` weren't stripped by whatever host you use; GitHub Pages serves them as-is, no changes needed.

---

## Requirements checklist

| Requirement | Status |
|---|---|
| RSVP form (name, attendance, guest count, message) | ✅ |
| RSVP auto-syncs to Google Sheets, new row per submission | ✅ (via Apps Script, step 3) |
| Exportable/manageable in Sheets | ✅ (native Sheets export) |
| Wishes wall shows name + message, near-real-time | ✅ (15s poll + instant local echo) |
| Countdown — days/hours/min/sec | ✅ |
| Clickable venue address → Google Maps | ✅ |
| Clickable date/time → Google Calendar with title/date/time/venue/description | ✅ |
| Placeholder wedding info, easy to replace | ✅ (single `CONFIG` object) |
| Elegant, modern, mobile-first, responsive, smooth scroll | ✅ |
| Opening cover / Bride & Groom / Countdown / Event details / Maps / RSVP / Wishes / Gift / Closing sections | ✅ |
| Floating music button, fade-in transitions | ✅ |
| Pre-wedding photos/video/music | ⚠️ Placeholder slots only — no media folder was attached to the conversation; see step 2 |
| Deployed to GitHub Pages | ⚠️ Requires your GitHub account — see step 4 (I can't push to your repo without access) |
