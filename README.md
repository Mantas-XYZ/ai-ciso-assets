# The AI CISO — public asset host

This repo exists for one reason: **Notion can only attach a file from a
world-readable HTTPS URL.** It is a CDN for rendered GIFs, nothing else.

Everything editorial lives in Notion. The render toolkit lives in the private
`ai-ciso-visuals` repo. This repo holds the rendered output and the workflow
that produces it.

---

## The URL contract

Every rendered asset is reachable at a predictable, permanent URL:

```
https://raw.githubusercontent.com/Mantas-XYZ/ai-ciso-assets/main/gifs/<name>.gif
```

That URL is what you hand to Notion's file-attachment API as `source_url`. It
serves with the right content type, needs no auth, and does not expire.

Files are laid out by year so the repo stays navigable:

```
gifs/2026/daily-2026-08-09.gif
gifs/2026/weekly-2026-w32.gif
```

---

## How a GIF gets here

Nobody commits by hand. The flow is:

1. Cowork finishes a daily or weekly newsletter in Notion.
2. Cowork fires a `repository_dispatch` at this repo with the card spec as the
   payload — headline, items, verdict. The spec is not stored anywhere; Notion
   stays the single source of truth.
3. The workflow checks out the private render toolkit with a read-only deploy
   key, renders the card with Pillow, and commits the GIF to `gifs/`.
4. Cowork reads back the raw URL and attaches it to the Notion page.

See `.github/workflows/render-card.yml`. Triggering is documented in the
Cowork skill that ships with the private repo.

---

## What must never go in here

This repo is public. Anything committed is world-readable immediately and
stays in git history even if deleted.

- **No unpublished copy.** A GIF rendered before the newsletter ships is
  readable by anyone who guesses the filename. If an issue is embargoed, render
  it on publication day, not before.
- **No source, drafts, or Notion IDs.** Those stay private.
- **No credentials.** The deploy key lives in Actions secrets, never in a file.

---

## Housekeeping

Cards run ~400–800 KB each. A daily cadence is roughly 200 MB a year, which is
comfortable — GitHub gets unhappy past about 1 GB. If it ever gets close,
prune old years rather than rewriting history.
