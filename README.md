# fawzii.me — Prank site

Simple static prank site: innocent landing → dodging button → jumpscare video → clown message.

## Contents

```
fawzii-prank/
├── index.html   ← main page (already created)
├── scare.mp4    ← YOU add this: 3-second jumpscare video
└── README.md    ← this file
```

## What you need to add: `scare.mp4`

A single video file with audio (loud jumpscare sound). Duration up to ~20 seconds — the code has a 25s safety fallback that force-shows the clown message even if the video hangs.

### Free video sources

| Source | Notes |
|---|---|
| [Pexels — horror](https://www.pexels.com/search/videos/horror/) | Royalty-free, no attribution required |
| [Pixabay — jump scare](https://pixabay.com/videos/search/scary/) | Same, no attribution |
| [Videvo — free](https://www.videvo.net/stock-video-footage/scary/) | Some tracks require attribution — check the license |

### Requirements
- **Format:** MP4 (H.264 codec, ~most common)
- **Duration:** up to ~20 seconds (matches the current safety fallback). If you want longer, bump the `setTimeout(showClownMessage, 25000)` value near the end of `index.html`.
- **Audio:** Yes, and loud. Half the jumpscare is the sound.
- **Resolution:** 720p or 1080p is plenty. Higher = bigger file, slower load.
- **Filename:** `scare.mp4` exactly (case sensitive on some systems)
- **File size:** aim for under 5 MB. For a 20-second video, run it through Handbrake or an online compressor first — otherwise slow phones may buffer for 5+ seconds before the video plays, killing the jumpscare effect.

### Making your own from an image + sound

If you can't find a video you like:

1. Grab a scary image from Unsplash / Pexels (jpg/png)
2. Grab a scream/roar sound from [Freesound](https://freesound.org/) or Pixabay
3. Combine in **any free video editor**:
   - [Kapwing](https://www.kapwing.com/) (browser, free, no signup for basics)
   - [Clipchamp](https://clipchamp.com/) (Microsoft, free, built into Windows 11)
   - [CapCut Web](https://www.capcut.com/) (free, good effects)
4. Export as MP4 (H.264, 720p or 1080p).
5. Rename to `scare.mp4` and drop it in this folder.

### Video optimization (optional, for faster load)

Once you have the mp4, run it through [Handbrake](https://handbrake.fr/) or an online compressor to shrink the file:
- Aim for < 2 MB (loads instantly, even on slow mobile connections)
- Preset: "Web Optimized" or "Fast 1080p30"

## Testing locally

Just open `index.html` in Chrome/Firefox:

```
File → Open File... → select index.html
```

Or on Windows, right-click the file → Open with → Chrome.

- **Desktop:** hover the button with your mouse — it should dodge 6 times, then let you click
- **Mobile:** load the URL on your phone — button should dodge 3 taps, then trigger

## Deploying on Cloudflare Pages (free)

1. [pages.cloudflare.com](https://pages.cloudflare.com) → Sign up
2. **Create a project** → **Upload assets**
3. Project name: `fawzii-prank`
4. Drag the entire `fawzii-prank` folder (`index.html` + `scare.mp4`) into the uploader
5. **Deploy** — wait ~30 seconds, Cloudflare gives you a preview URL like `fawzii-prank.pages.dev`
6. Open it in a new tab — verify everything works

## Attaching the `fawzii.me` domain

Still inside your Cloudflare Pages project:

1. **Custom domains** → **Set up a custom domain**
2. Enter: `fawzii.me`
3. Cloudflare will show you the DNS records to add
4. Go to **Namecheap → Domain List → fawzii.me → Manage → Advanced DNS**
5. Add the records Cloudflare gave you (usually a CNAME to `something.pages.dev`, or A records)

**⚠️ Do NOT touch the `amana.fawzii.me` A record** — that one points to the Amana bot's EC2 (`3.122.8.207`). Only add/change records for the root `@`.

DNS propagation takes 5–60 minutes. When `https://fawzii.me` loads the prank site, you're done.

## Safety notes

- Jump scares can trigger panic attacks, cardiac events (rare), and are dangerous for people with photosensitive epilepsy if the video contains flashing.
- Best used on friends who know your sense of humor.
- Don't spam the URL to strangers — it stops being funny fast.
- If you ever want to disable it, just change the Cloudflare Pages custom domain back to a "coming soon" page.

## Customization ideas

Open `index.html` and search for these snippets:

- **`LABELS`** array — change the button text sequence as the user tries to click
- **`MAX_DODGES`** — how many times the button runs (currently 6 on desktop, 3 on mobile)
- **`msg-text`** — change "بتعمل ايه هنا يا كلاون؟" to whatever message you want
- **`msg-sub`** — the smaller subtitle text
- **CSS `background`** on `.message` — change the red gradient to any color you like
- **Clown message timing** — waits for the video's `ended` event by design. A stall watchdog auto-adapts to the video's real duration (recomputes on every `playing` event, including recovery from mid-video buffer stalls). Only fires if playback truly freezes for longer than "remaining duration + 5s". You don't need to hard-code any timeouts — it works for any video length.

Enjoy scaring your friends 🎪


## Handling low-resolution source video (e.g. 240p)

If your `scare.mp4` is low resolution (240p, 360p), the site is set up to display it well:

- The **foreground** video is shown at its native aspect ratio, centered. Never upscaled aggressively.
- A **blurred, muted duplicate** of the same video fills the background. This adds cinematic atmosphere and hides the low-resolution ugliness that would otherwise happen from fullscreen stretching.

You don't need to do anything special — just drop `scare.mp4` in and it works. If you want to tweak the look:

- **CSS `.scare-bg`** — change the `filter: blur(28px) brightness(0.55) saturate(1.4)` values:
  - Larger `blur()` → more abstract backdrop
  - Lower `brightness()` → darker, moodier
  - Higher `saturate()` → more color intensity
- **CSS `#scareVideo`** — remove `box-shadow` if you don't want the vignette around the video edges.
