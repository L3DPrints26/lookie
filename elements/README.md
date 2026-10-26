# Bundled Elements

Anything you put in this folder ships **inside Lookie**. It shows up in
**Elements → Graphics**, and users can drop it on the timeline without
importing anything of their own.

## Adding an asset

1. Drop the file in this folder (or a subfolder — your choice).
2. Add one entry to the `assets` array in `manifest.json`.
3. Reload the app. That's it — no code changes, no rebuild step.

```json
{
  "file": "confetti.webm",
  "label": "Confetti Burst",
  "category": "Overlays",
  "kind": "video",
  "duration": 3,
  "tags": ["party", "celebration", "birthday"]
}
```

| field      | required | what it does                                                        |
| ---------- | -------- | ------------------------------------------------------------------- |
| `file`     | yes      | filename, relative to this folder                                    |
| `label`    | yes      | what the user sees, and what search matches first                    |
| `category` | no       | groups it under a chip. Add new ones to `categories` above           |
| `kind`     | no       | `image` \| `video` \| `audio`. Guessed from the extension if omitted |
| `duration` | no       | seconds on the timeline. Images default to 4s; video uses its own    |
| `tags`     | no       | extra search words — worth filling in, it's what makes search useful |

## Which formats to use

**Still images — PNG (with transparency), JPG, WebP.**
These just work. A transparent PNG is the right choice for logos,
watermarks, frames and stickers.

**Not SVG.** It previews fine in the browser but the export encoder can't
decode it, so an SVG asset would look right in the app and then vanish from
the finished MP4 — the worst kind of bug. Export it to PNG at 2x the size
you need and use that.

**Animated — use WebM or MP4, not GIF.**
This matters. Lookie imports a GIF as a **single still frame**, so an
animated GIF will not animate. Convert it first:

```bash
ffmpeg -i sticker.gif -c:v libvpx-vp9 -pix_fmt yuva420p -an sticker.webm
```

`yuva420p` is the bit that keeps transparency, so the sticker sits over
footage instead of on a black box. VP9-in-WebM is the only widely supported
combination that carries an alpha channel — MP4/H.264 cannot, so an MP4
overlay will always have a solid background.

**Audio — MP3 or WAV.** Lands on an audio track.

## Keep an eye on size

Every file here is bundled into the installer and loaded by the app, so it
costs every user the download whether they use it or not. A few MB of
genuinely useful overlays is fine; a 200MB stock library is not. Compress
before adding — `-crf 35` on a WebM overlay is usually invisible and a
fraction of the size.

## If something doesn't show up

- The manifest is JSON, so a trailing comma anywhere breaks the whole file.
  The app logs a clear parse error to the console and falls back to showing
  no bundled assets rather than crashing.
- Filename mismatches are case-sensitive once packaged, even though Windows
  is forgiving in dev.
