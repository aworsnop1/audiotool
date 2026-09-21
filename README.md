# Exam Audio Player

A single-file, no-backend listening-exam player. Once a student presses **Start**, the audio
plays through to the end. There is no pause, no rewind, no fast-forward, no speed control.

`index.html` is **both** the teacher's builder tool and the student's player. Which one you get
depends on the URL:

| URL | What loads |
| --- | --- |
| `https://your-site/` | The builder — a 4-step wizard for teachers |
| `https://your-site/?help` | A printable step-by-step guide for teachers |
| `https://your-site/?c=…` | The locked player, configured by that link |
| `https://your-site/?c=…&preview=1` | Same player, but plays aren't counted (teacher testing) |

**You deploy this once.** Teachers make as many exams as they like by generating new links —
there is no second tool to build, no database, no per-subject copy to maintain. The whole
configuration (audio URL, number of plays, timings, wording) is packed into the link itself.

---

## 1. Deploy it (you, once — about 5 minutes)

Pick whichever is easiest for your school. All are free at this scale.

### Cloudflare Pages (recommended)
1. Go to Cloudflare Pages → **Create a project** → **Direct upload**.
2. Drag in the folder containing `index.html`.
3. You get `https://something.pages.dev`. Attach a custom domain such as
   `listening.diocesan.school.nz` under **Custom domains**.

### Netlify
1. Go to <https://app.netlify.com/drop> and drag the folder in.
2. Rename the site and/or attach a custom domain in **Site settings**.

### GitHub Pages
1. Create a repo, upload `index.html`, then **Settings → Pages → Deploy from branch → main / root**.

### Your existing school web server
Copy `index.html` anywhere it is served over HTTPS. That's it — no build step, no dependencies.

> Use a **short, memorable, permanent** URL. Teachers will paste it around for years.

---

## 2. Whitelist it in DigiExam (you, once)

DigiExam locks students to approved URLs, so add:

1. **The player domain**, e.g. `listening.diocesan.school.nz`
That is the only entry needed — the recordings are served from the same domain. See the next
section for why they have to be.

---

## 3. Where the audio lives

**In an `audio/` folder next to `index.html`.** Not in a cloud drive.

### Consumer cloud drives cannot work, and it isn't a sharing setting

Google Drive, OneDrive and Dropbox all serve files with:

```
cross-origin-resource-policy: same-site
```

Browsers enforce CORP independently of CORS, so the file is blocked from loading into any page not
on that provider's own site — regardless of `Access-Control-Allow-Origin: *`, and regardless of how
the file is shared. Verified against a real "Anyone with the link" Drive file: it returns
`200 audio/mpeg` with a valid MP3 body to `curl`, and is refused by the browser.

The builder now detects these links and says so plainly instead of blaming the sharing settings.

### Same-origin hosting, with a self-service picker

The builder reads the `audio/` folder through the public GitHub contents API
(`api.github.com/repos/OWNER/REPO/contents/audio`) and shows teachers a **list of recordings to
click**. They never see a URL. The repo is auto-detected from a `*.github.io` hostname; on a custom
domain, set `AUDIO_REPO` near the middle of `index.html`.

Because the audio is same-origin, the player can also pre-download the whole file into memory before
enabling Start, so a mid-exam network hiccup can't spoil a play.

**Other hosts** work if they don't set a restrictive CORP header — Cloudflare R2 with a public
bucket is the obvious upgrade if you outgrow GitHub. The "paste a link" box remains for that.

**Audio format:** MP3 or AAC/M4A, mono, 96–128 kbps is plenty for speech and keeps files small.
Avoid WAV and FLAC. GitHub's per-file limit is 100 MB.

### The privacy tradeoff, stated plainly

Anything GitHub Pages publishes is fetchable by anyone who knows the URL — that is how students'
browsers get it, so no static host can avoid it. A private repo hides the *source*, not the
*published site*. Mitigate with unguessable filenames and by deleting recordings after the exam
period. For genuinely high-stakes material, you need a host with real access control, not a static
site.

---

## 4. What teachers do

**Don't write your own instructions — they're built in.** Send teachers two things:

1. The tool's address, e.g. `https://listening.diocesan.school.nz`
2. The words *"click **Step-by-step instructions** at the top right if you get stuck"*

That link opens a full printable guide (`?help`) covering Drive sharing, each wizard step, testing,
pasting into DigiExam, and a troubleshooting section. It lives in the same file, so it can never go
out of date. Print it for a staff handout if you prefer paper.

The wizard itself is four screens, one question at a time:

| Step | What the teacher does |
| --- | --- |
| 1. Name | Types the heading students will see |
| 2. Audio | **Clicks their recording from a list** read live out of the `audio/` folder. No URLs. A *How do I add a new recording?* panel links straight to the folder on GitHub |
| 3. Rules | Picks from cards: *Once / Twice / Three times / Unlimited*, reading time, gap between plays |
| 4. Link | Gets a plain-English summary of what they built, a **Copy link** button, a **Try it out** button, and the DigiExam steps |

Two things worth knowing:

- **They cannot get past step 2 with a broken link** without explicitly confirming they want to.
  This catches the single most common mistake — a Drive file left on *Restricted*.
- **Links are editable.** On step 1, *"Editing an existing link? Paste it here"* loads every setting
  back out of an old link. Teachers change what they need and generate a fresh one.

Changing an exam means generating a new link and replacing it in DigiExam. Old links keep working,
so nothing breaks mid-exam.

---

## 5. What the lock actually does

| Attempt | Result |
| --- | --- |
| Seek forward (any method) | Snapped back to the current position, playback continues |
| Rewind | Snapped back to the current position |
| Pause, or the space bar | Playback resumes immediately |
| Arrow keys, Home/End, Page Up/Down | Swallowed while playing |
| Headphone / keyboard media buttons | Handlers overridden with no-ops |
| Change playback speed | Reset to 1× |
| Right-click the audio | Context menu blocked |
| Reload the page mid-play | That play is counted as used, with a warning prompt first |
| Close the tab and come back | Play count persists for 6 hours |
| Edit the numbers in the URL | Link fails its integrity check and refuses to play |

All of these were verified in a browser against a real audio file.

### Be clear-eyed about the threat model
This is a **classroom control, not a cryptographic one.** Everything runs in the browser, so
someone with developer tools open could defeat it. The real barrier is DigiExam's lockdown
environment; this player is what stops ordinary fiddling — the accidental pause, the "just let me
hear that bit again", the extra replay — and makes every student's experience identical.

### The 6-hour play counter
Play counts are stored in the browser and expire after 6 hours. Two consequences:
- A student who refreshes or reopens the page does **not** get extra plays. Good.
- If two different students sit the same exam on the *same device* within 6 hours, the second one
  will find the plays used up. Clear the browser's site data between sittings, or use per-student
  devices (the normal DigiExam setup).

---

## 6. Before the first real exam

- [ ] Player domain whitelisted in DigiExam, and the audio domain too if different
- [ ] Run one full rehearsal inside DigiExam on a student device, with headphones
- [ ] Check the audio is loud enough — the player deliberately offers no volume boost
- [ ] Tell students beforehand that it cannot be paused, and to have pen and paper ready
- [ ] Have a fallback: a supervisor-controlled speaker copy of the audio, in case of device trouble

---

## Files

- `index.html` — the whole thing. No dependencies, no build step, works offline as a file.
- `test-tone.wav` — a 12-second warbling tone for testing the setup. Delete it before deploying.

## Editing it

Everything is in one file: CSS at the top, then three HTML blocks (`#builder`, `#help`, `#player`),
then the JavaScript. A tiny router at the top of the script picks which block to show.

- **Teacher wording** lives in the `#builder` and `#help` HTML — plain text, safe to reword.
- **The player's logic** is a small state machine: `loading → ready → reading → playing → gap →
  finished`.
- **The lock** is the cluster of `audio.addEventListener` calls under the *"lock down every way of
  scrubbing"* comment.
- **The 6-hour play counter** is `TTL_MS` in `startPlayer`.
