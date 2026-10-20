# শিক্ষক আবেদন · Teacher Application

The teacher application form — a 10-step form (identity → family → contact → education → experience →
subjects → suitability → background → documents → declaration) that writes to the institute's
Firebase database and shows a `TCH-…` application ID on success.

This folder is the **built** site: plain static files, no build step, no npm. Upload it and it runs.

```
teacher-portal/
├── index.html                              entry page (0.7 KB)
├── assets/
│   ├── index-*.js                          the app (263 KB)
│   ├── index-*.css                         all styles (120 KB)
│   ├── Logo-01-web-*.png                   the seal (170 KB)
│   └── confetti.module-*.js                success-screen confetti (11 KB)
└── README.md                               this file
```

≈ 565 KB total. All paths are relative (`./assets/…`), so it works at any URL depth.

---

## 1. Publish it

1. Create a new **public** repository named **`teacher-form`**
   *(the application portal already links to `https://jamiaislamia.github.io/teacher-form/` —
   if you pick another name, tell me and I'll update that one link).*
2. Upload **everything in this folder**, keeping the structure:
   `index.html` at the repository **root**, and the `assets/` folder beside it.
3. Repository → **Settings → Pages** → *Source*: **Deploy from a branch** →
   branch **`main`**, folder **`/ (root)`** → **Save**.
4. After ~1 minute the form is live at:

   ```
   https://jamiaislamia.github.io/teacher-form/
   ```

No `.nojekyll`, no workflow, no Actions — the filenames contain no underscore, so Jekyll leaves
them alone.

> **Preview locally:** don't double-click `index.html` — browsers block ES-module scripts on
> `file://`. Serve the folder instead:
> `cd teacher-portal && python -m http.server 8080` → <http://localhost:8080/>
> (On GitHub Pages this is a non-issue: everything is served over https.)

> Do **not** upload the source folder (`website/Teacher registration/`) — it is 180 MB because of
> `node_modules`. Only these five files are needed.

---

## 2. What the form does

| Step | Saves to |
|---|---|
| every step | `localStorage` draft (so a refresh does not lose the application) |
| **Submit** | `POST /teacher-applications` on `sms-admission-4f632-default-rtdb` |
| success screen | shows the generated `TCH-XXXXXXXX` ID, printable, copyable |

* The photo is downscaled to 360×480 JPEG in the browser before it is sent, so a phone photo does
  not blow up the record.
* If the network fails, the application is kept on the device and a **retry** button appears — it is
  never silently lost.
* Read-only tracking of the ID lives in the **apply portal** (`…/apply-portal/`), not here.

**Requirement:** the database rules must allow writes on `/teacher-applications`. The project's
`database.rules.json` declares `"teacher-applications": { ".read": true, ".write": true }` — deploy
that with `firebase deploy --only database` if you have not already.

---

## 3. After it is live

Tell me the URL (or confirm it is `…/teacher-form/`) and I will:

* point the portal's **শিক্ষক আবেদন** card at it (already set to `/teacher-form/`, so probably no change),
* set `teacherForm` in the website's `assets/js/site-links.js`, so the site's teacher links stop
  falling back to the built-in form.

Until then, the portal's teacher card opens `/teacher-form/`, which 404s — that is expected and is
the only link left to satisfy.

---

## 4. Design notes

* Same dark family as the student admission form and the apply portal: navy `#0a0e1a`, indigo
  `#5c6ef8`, Cormorant Garamond headings, DM Mono micro-labels, Hind Siliguri for Bangla.
* One accent per screen; status colour only on validation messages, the success tick and errors.
* The step tracker is 1→10 and clickable; validation errors appear inline under the field (no
  pop-ups), the first bad field is scrolled into view and highlighted.
* Works at 320 px wide; `prefers-reduced-motion` disables the confetti and transitions.
