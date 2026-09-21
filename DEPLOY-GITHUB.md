# Putting the Exam Audio Player on GitHub Pages

No software to install, no command line. Everything below happens in a web browser.
Allow about ten minutes. You only ever do this once.

You are uploading `index.html` — the entire tool, builder, teacher guide and player — and creating
an `audio` folder next to it for the MP3s.

> **Why the MP3s go here and not in Google Drive.** Google Drive, OneDrive and Dropbox all send a
> `Cross-Origin-Resource-Policy: same-site` header, which browsers enforce independently of any
> sharing setting. It stops their files being played by any other website. There is no way round it
> from a web page — so the recordings live alongside the tool, where they always work and load
> faster.

---

## Step 1 — Sign in to GitHub

Go to <https://github.com> and sign in, or create a free account.

> **Note for later:** your site's address will contain your username, e.g.
> `https://jsmith.github.io/audiotool/`. Every DigiExam link will point at it. If you ever leave
> the school, those links break. Step 7 shows how to avoid that with a school domain — worth doing
> before you hand the tool out widely.

---

## Step 2 — Create the repository

Your repository is called **audiotool**. If you have already created it, skip to Step 3 — but first
check one setting, described in point 3 below.

1. Click the **+** in the top-right corner, then **New repository**.
2. **Repository name:** `audiotool`
   All lowercase. GitHub Pages addresses are case-sensitive, so an all-lowercase name means nobody
   can mistype it later.
3. **Public / Private:** choose **Public**.
   GitHub Pages only works on private repositories with a paid plan. Public is fine here — the file
   you are uploading contains no exam material, just the player itself.
   *(Already created it as Private? **Settings → General**, scroll to the bottom, **Change
   repository visibility**.)*
4. Tick **Add a README file**.
5. Click **Create repository**.

Your address will be `https://YOUR-USERNAME.github.io/audiotool/`.

---

## Step 3 — Upload index.html

1. On your new repository page, click **Add file** (near the green *Code* button), then
   **Upload files**.
2. Drag `index.html` into the box, or click *choose your files* and select it.
3. Scroll down and click **Commit changes**.

You should now see `index.html` listed in the repository.

---

## Step 3b — Create the audio folder

GitHub has no "new folder" button, and it cannot store an empty folder. A folder comes into
existence the moment you commit a file whose path contains it. Either method below does that.

### Method 1 — drag a folder (quickest if you have an MP3 ready)

1. On your own computer, make a folder called exactly `audio` and put your MP3 inside it.
2. In the repository, click **Add file** → **Upload files**.
3. Drag the whole **`audio` folder** — not the file — into the drop area. GitHub keeps the folder
   structure.
4. Click **Commit changes**.

### Method 2 — type the folder name into a filename (works with no MP3 to hand)

1. In the repository, click **Add file** → **Create new file**.
2. In the filename box, type exactly:

   ```
   audio/README.md
   ```

   As soon as you type the `/`, GitHub turns `audio` into a folder — you will see it appear as a
   separate crumb before the filename box.
3. In the large text box underneath, type a line such as `Exam recordings go in this folder.`
4. Click **Commit changes…**, then **Commit changes** again.

*Create new file* only makes text files, which is why this creates a small README rather than the
MP3 itself. The README is harmless — the tool ignores anything that is not an audio file.

### Adding recordings from then on

Click into the `audio` folder first, then **Add file** → **Upload files**, drag the MP3 in, and
**Commit changes**. Because you are already inside the folder, the file lands in the right place.

The tool lists whatever is in there automatically — you never have to edit `index.html` again.

> **Worth knowing before you put real exam material in.** Anything published by GitHub Pages is
> reachable by anyone who knows or guesses the address, even if the repository itself is private.
> That is how the students' browsers get the file, so it cannot be avoided on a free static site.
> In practice: use filenames nobody would guess, and delete recordings after the exam period.
> If that is not acceptable for your highest-stakes papers, tell me and we will look at a host with
> proper access control.

---

## Step 4 — Switch on GitHub Pages

1. Click **Settings** (the tab along the top of the repository).
2. In the left-hand menu, click **Pages**.
3. Under **Build and deployment → Source**, choose **Deploy from a branch**.
4. Under **Branch**, set the dropdown to **main** and leave the folder as **/ (root)**.
5. Click **Save**.

---

## Step 5 — Wait, then find your address

The first publish takes one to two minutes. Refresh the Pages settings screen until a green banner
appears reading *"Your site is live at …"*.

Your address will look like:

```
https://YOUR-USERNAME.github.io/audiotool/
```

**Bookmark it. This is the address you give to teachers.**

> If you see a 404 page, wait another minute and refresh. It is almost always just the first build
> still running.

---

## Step 6 — Test it

1. Open your new address. You should see **Step 1 of 4 — Name the recording**.
2. Click **Step-by-step instructions** in the top right. The teacher guide should appear.
3. Go back to Step 2 of the wizard. The MP3 you uploaded in Step 3b should appear in the list.
   Click it, finish the wizard, then press **Try it out first** and listen right through.

If all three work, you are done. Skip to Step 8.

---

## Step 7 — Optional but recommended: a school address

This replaces `jsmith.github.io/audiotool` with something like `listening.diocesan.school.nz`.
It is shorter and easier to dictate, and it means you can move the site anywhere later — to a
school account, or off GitHub entirely — **without breaking a single DigiExam link**.

Ask whoever manages your school's DNS to add one record:

| Type | Name | Value |
| --- | --- | --- |
| CNAME | `listening` | `YOUR-USERNAME.github.io` |

Then in GitHub: **Settings → Pages → Custom domain**, type `listening.diocesan.school.nz`, and
click **Save**. Once it verifies, tick **Enforce HTTPS**.

> **One extra step if you use a custom domain.** The tool works out which repository to list
> recordings from by reading its own `github.io` address. On a custom domain it cannot do that, so
> tell it directly: open `index.html`, click the pencil icon, find this line near the middle of the
> file —
>
> ```js
> var AUDIO_REPO = "";        // e.g. "jsmith/audiotool"
> ```
>
> — and put your username and repository between the quotes, e.g. `"jsmith/audiotool"`. Commit the
> change. Without this, teachers see the "paste a link" box instead of the list of recordings.

Do this *before* teachers start building links, or you will have to rebuild them all.

---

## Step 8 — The DigiExam whitelist

DigiExam only lets students open approved websites. Ask your exams administrator or IT team to
allow one address:

- `YOUR-USERNAME.github.io` (or your custom domain from Step 7)

That is the only one needed, because the recordings are served from the same address as the player.

**This is a one-off job.** Once done it covers every exam any teacher ever builds. The tool reminds
teachers of this on its final screen, but they should not need to ask again after the first time.

---

## Step 9 — Give teachers access, then hand it over

Teachers need to be able to add MP3s to the `audio` folder. Two ways to arrange that:

**Option A — add them as collaborators (self-service).**
**Settings → Collaborators → Add people.** They need a free GitHub account, and you add them once.
After that they upload their own recordings and never need you again — which was the whole point.

**Option B — you upload for them.** Teachers email you the MP3, you drop it in the `audio` folder.
Fine if it is only a handful of staff, but it puts you back in the loop for every exam.

Then send staff two things:

1. The address from Step 5 or 7.
2. *"Click **Step-by-step instructions** at the top right if you get stuck."*

That guide is built into the page, covers adding a recording through to troubleshooting, and can be
printed for a staff handout. You should not need to write anything yourself.

---

## Updating the tool later

If you are given a newer `index.html`:

1. Open your repository and click on `index.html`.
2. Click the **pencil** icon (top right of the file view).
3. Delete everything in the editor, paste in the new contents, and click **Commit changes**.

Or use **Add file → Upload files** again with a file of the same name — it will replace the old one.

Changes go live in about a minute. **Links teachers have already built keep working** — the settings
live inside the link, not on the server. Press Ctrl+Shift+R (Cmd+Shift+R on a Mac) if you still see
the old version.

---

## Common problems

**"404 — There isn't a GitHub Pages site here."**
Three possible causes, in the order worth checking:
1. The first build has not finished — wait a minute and refresh.
2. Step 4 was not saved. Check **Settings → Pages** shows Branch: `main`, folder: `/ (root)`.
3. **You renamed the repository after switching Pages on.** Go to **Settings → Pages**, set the
   branch to *None*, **Save**, then set it back to **main** / **/ (root)** and **Save** again. That
   forces a rebuild at the new address.

**The page loads but looks like plain text.**
The file was uploaded with the wrong name. It must be exactly `index.html`, all lowercase — not
`Index.html` or `index.html.txt`.

**Pages is missing from Settings, or greyed out.**
The repository is set to Private on a free account. Go to **Settings → General**, scroll to the
bottom, and use **Change repository visibility** to make it Public.

**The Copy link button does nothing.**
Only happens on insecure connections. Make sure you are using the `https://` address, not `http://`.

**A teacher's link says "Link problem".**
The link was cut short when copied — these links are long. Copy the whole thing again.
