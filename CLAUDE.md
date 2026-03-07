# Video-to-Docs — Claude Code Instructions

> **Setup**: Place this file in your project root as `CLAUDE.md`, or at `~/.claude/CLAUDE.md`
> to apply it globally across all Claude Code / VS Code sessions.
>
> **⚠️ If a `CLAUDE.md` already exists** at the destination: don't replace it — append
> this file's contents as a new section at the bottom instead.

## When to use this

When the user provides a video file (`.mp4`, `.mov`, `.webm`, `.mkv`, `.avi`) and asks
to generate documentation, notes, a guide, or any written output from it, follow the
workflow below. Trigger phrases include: "turn this video into docs", "transcribe and
format this recording", "make a markdown file from this video", "create documentation
from my screen recording".

## Repository structure

This skill is designed for the USAHello MkDocs/Netlify docs repo:

```
repo-root/
├── docs/               ← all content lives here
│   ├── img/            ← ALL screenshots go here (shared across all pages)
│   ├── <section>/      ← section subdirs contain .md files and source videos
│   └── *.md            ← top-level pages live directly in /docs/
├── mkdocs.yml          ← update this manually after adding a new page
└── ...
```

## Dependencies

- **ffmpeg**: `brew install ffmpeg` or `apt install ffmpeg`
- **Whisper**: `pip install openai-whisper`

## Glossary — always apply these corrections

After transcription, correct these terms throughout before building the document:

| Correct form | Common mistranscriptions |
|---|---|
| USAHello | USA Hello, Usa Hello, US Hello, usahello |
| FindHello | Find Hello, find hello |
| MkDocs | mk docs, mkdocs, MKDocs |
| Netlify | net-ify, Net-ify |

Add to this table as new terms are identified.

## Workflow

### 1. Establish paths

Ask the user to confirm:
- Which `/docs/` subdirectory this page belongs to, or whether it's a top-level page
- The desired `.md` output filename (default: video filename slugified, e.g. `setting-up-workspace.md`)

Then derive:
```bash
VIDEO_SLUG="<slugified-video-name>"         # e.g. setting-up-workspace
DOCS_DIR="<repo-root>/docs"
IMG_DIR="$DOCS_DIR/img"
SECTION_DIR="$DOCS_DIR/<section>"           # or $DOCS_DIR if top-level
MD_OUTPUT="$SECTION_DIR/$VIDEO_SLUG.md"
```

Determine the correct image path prefix based on page location:
- Page in a **subdirectory** (`/docs/<section>/page.md`): use `../img/`
- Page at **top level** (`/docs/page.md`): use `img/`

### 2. Extract audio

```bash
ffmpeg -i <video-file> -ac 1 -ar 16000 -vn /tmp/audio.wav -y
```

### 3. Transcribe with timestamps

```bash
whisper /tmp/audio.wav --model base --output_format json --output_dir /tmp
```

Produces `/tmp/audio.json` with timestamped segments. If Whisper isn't available, ask
the user to paste a transcript.

**After transcription, apply all glossary corrections** to every segment before proceeding.

### 4. Parse narrator cues → document structure

| Spoken phrase | Markdown output |
|---|---|
| `heading: <text>` / `section: <text>` | `## <text>` |
| `subheading: <text>` / `subsection: <text>` | `### <text>` |
| `note: <text>` / `important: <text>` | `> **Note:** <text>` |
| `warning: <text>` / `caution: <text>` | `> ⚠️ **Warning:** <text>` |
| `bullet: <text>` / `list item: <text>` | `- <text>` |
| `step N` / `step N: <text>` at segment start | auto-numbered list item |
| `first`, `second`, `third`… at segment start | numbered list items |
| `screenshot` anywhere in segment | capture frame → embed with contextual filename (see step 5) |
| `scratch that` anywhere in segment | discard this segment and the one immediately before it |

**Suggested screenshots:** Also watch for "as you can see", "take a look at", "here we
have", "notice that", "look at this" — capture the frame but embed as an HTML comment:
`<!-- ![<description>](<img-path>/<filename>.jpg) -->`

**Retraction:** "scratch that" drops that segment and the preceding one (including any cue).

Group consecutive bullet/list cues into a single Markdown list block. Unmatched segments
become prose.

### 5. Extract screenshots

All screenshots go to `/docs/img/`. Name them contextually using the video slug and a
brief description of what's visible at that moment:

**Format:** `<video-slug>-<description>.jpg`
**Examples:** `setting-up-workspace-conda-install.jpg`, `email-notifications-toggle.jpg`

Use surrounding transcript text to infer a meaningful description. Fall back to
`<video-slug>-step-<N>.jpg` if no clear context is available.

```bash
mkdir -p "$IMG_DIR"
ffmpeg -i <video-file> -ss <timestamp> -frames:v 1 -q:v 2 \
  "$IMG_DIR/<video-slug>-<description>.jpg"
```

### 6. Write the .md file

```markdown
# <Inferred title from first 30s of transcript>

## Overview
<First paragraph, lightly edited for clarity>

## <Section from heading cue>
<Prose...>

![<Alt text describing what's shown>](../img/<video-slug>-<description>.jpg)
```

Save to `$MD_OUTPUT`. Confirm the full path to the user when done.

Remind the user to add the new page to `mkdocs.yml` manually.

## Style and quality notes

- Remove filler words ("um", "uh", "like", "you know") from all prose
- Tighten transcribed speech — edit for clarity while preserving meaning
- Screenshot alt text should describe what's on screen, not just say "screenshot"
- Infer the document title from the first 30 seconds if not explicitly stated
- If audio is missing or transcription fails, ask the user to provide a transcript manually
- When in doubt about a garbled cue word, use best judgment to interpret the intent

## Tips for better recordings

- **Open with a title**: "This is a guide to [X]" — seeds the document title
- **Say cues explicitly**: "heading:", "step 1:", "note:" — don't rely on pauses for structure
- **Pause around cues**: 1–2 seconds before and after helps Whisper transcribe them cleanly
- **Say "screenshot" at the right moment**: speak it when the screen shows what you want captured
- **Use "scratch that" to retract**: say it after a mistake and re-state — the skill drops both
- **One topic per recording**: each video becomes one `.md` page in MkDocs
