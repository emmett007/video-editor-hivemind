---
name: video-editor-hivemind
description: The shared skill every video-format skill loads first: learning loop and edit log, standing rules, Frame.io delivery and revision rounds, b-roll sourcing, Animation Composer, bridge and computer-use gotchas, general preferences, borrowed craft rules. Load it directly for any editing question that isn't tied to one format.
---

# Video Editor Hivemind (Premiere Pro)

Base layer for all of the creator's Claude-driven edits. Format skills (`example-lsn-longform`, `example-lsn-shorts`, your own) load this file first and add only their own layout, pacing and assets. A rule lives in exactly one place: general rules here, format rules there.

The first version of this file was seeded by scouring X for the best and most-shared Claude video-editing capabilities and folding them in. Step 6 below is how that keeps happening: every new capability the creator sends lands here once and cascades to every format.

## 0. The learning loop (the creator's standing instruction)

> "every time you learn something new, permanently add it to the skill." (the creator, 2026-10-21)

Skills can't be edited silently; every save goes through a review card. So the permanent memory between skill versions is a **living log on the creator's drive**:

**`<DRIVE>/<channel>/Claude outputs/Edit-Log.md`**
From `device_bash` it's `"$HOME/mnt/<channel>/Claude outputs/Edit-Log.md"`. Run `ls $HOME/mnt/` first, because the mount name can differ. If the folder isn't connected, request `<DRIVE>/<channel>` with `device_request_folder_access`. Never reconstruct lessons from memory.

1. **At the start of every editing session**, `cat` the whole log with `device_bash`, before touching Premiere. Its lessons override every skill file wherever they conflict, because they're newer.
2. **The moment I learn something, append it.** Don't wait for the end of the session, because it may get compacted. Something counts if the creator corrects me, a tool fails in a new way, a workaround works, I find a new convention, or the creator states a preference. One line per lesson, tagged with the skill it belongs to:
   `- YYYY-MM-DD [skill/area] lesson — source (the creator said / observed in J##)`
   Skills: `hivemind`, `longform`, `shorts`, or a new format's name. Areas: setup, cut, broll, gfx, transitions, audio, markers, export, bridge, computer-use, preference.
   Append with `cat >> "…/Edit-Log.md" <<'EOF' … EOF`. Never rewrite history. If a lesson was wrong, add a dated correction.
3. **Where a lesson goes.** Bridge, computer-use, Frame.io, Animation Composer, QA, playbook rules and the creator's general preferences → the Hivemind (this file), so every format inherits it. Frame size, layout numbers, pacing, brand assets, recipes for one kind of video → that format's skill. A format-level trick that turns out to be general gets **promoted**: add it here and delete it from the format file on the same fold.
4. **Fold the log into the skills.** At the end of a working session with new lessons, or when the creator asks: propose an updated full SKILL.md with `propose_skills` (kind improvement) for each skill that has new lines (`video-editor-hivemind`, each format skill, …). Then append `- YYYY-MM-DD folded into <skill> vN (lessons up to here)` to the log. Tell the creator in one line which cards are waiting; if they are on a phone, cards don't render, so say they're for the desktop.
5. **After a human editor finishes a job**, study the finished timeline itself, not just the export (section 4, "Learning from a finished timeline"), and turn every difference into a log line.
6. **Integrating a new capability the creator sends** (an X post, a repo, a skill, a tool). Standing job, no need for the creator to specify where it goes:
   1. Pull the full text/code down and save it verbatim to `Claude outputs/<slug>.md` (or a folder) so nothing is lost if the post disappears. X blocks fetches from the sandbox: use Chrome on the creator's laptop.
   2. Sort it: a **tool or method** (bridge command, QA script, model, Frame.io trick) or a **craft rule that applies to every format** → Hivemind. A rule for **one frame shape or one kind of video** → that format's skill; say which one was picked so they can overrule it. A **paid service, not a skill** → one log line, nothing else.
   3. Add a row to the **capabilities index** (section 1) saying when to reach for it. Knowing it exists is not the same as using it at the right step.
   4. **Walk every format recipe once** and, at each step the capability changes, add a one-line pointer to the Hivemind section (never a copy of the method). If no step changes, say so and leave the recipes alone.
   5. Propose the cards (the Hivemind, plus each format touched), append the log line, tell the creator what landed where.

## 1. The skill family

This file is the **base**. It holds everything that is true no matter what is being cut. Format skills load it first and add only what is different about their format:

| Skill | Use it for |
|---|---|
| `example-lsn-longform` | Longevity Science News 16:9 episodes: J-job setup, A-roll rough cut, news-anchor graphics layout, intro/outro GFX, music, finish checklist |
| `example-lsn-shorts` | 30–60 s vertical (9:16) shorts cut from an LSN long form |
| (yours) | one file per format you cut: podcast clips, ads, a second channel… |

When the creator asks for an edit, pick the format skill, load it, and it will tell you to load this one. For bridge installs and general Premiere/AE scripting, also load `adobe-premiere-after-effects`.

**Capabilities index.** One row per capability, with the moment to reach for it. A new format built from the template inherits this awareness, not just the methods. Add a row every time something new lands.

| Capability | Reach for it when | Where |
|---|---|---|
| Premiere bridge (leancoderkavy MCP) | Anything the timeline can do by API: cuts, inserts, Motion, markers, exports, review frames | section 7 |
| Computer use on Premiere | Panels with no API only: Animation Composer, Essential Sound, Track Mixer inserts, Frame.io V4 panel, native modals | section 8 |
| Transcript → timeline map | Picking takes, placing stills on the spoken beat, finding shorts, verifying a cut | format recipes |
| Frame.io V4 panel | Every delivery and every revision round; reading the creator's notes | section 4 |
| Internet Archive / source-video stills | A beat needs footage or a picture the job folder doesn't have | section 5 |
| Animation Composer Zoom In/Out | Into and out of brand GFX and other hard visual breaks | section 6 |
| Creator Edit Playbook (claims file, no-flicker, QA pass, thumbnails, titles) | Before graphics (claims), before calling a cut done (QA), when asked for thumbnails or copy | section 10 |
| Independent reviewer agent | Every final, before delivery: a fresh agent grades against the format's checklist | section 10 |
| Face detect on exported frames | Any reframe (shorts, punch-ins) to confirm where the creator's face landed | section 7 |

**Inheritance runs one way**: every format skill loads this file; no format skill is built on another one. **New format**: start from the blank template (`skills/format-template`, or `Claude outputs/format-skill-TEMPLATE.md` on the drive), fill in only what is different about that format, propose it as a new skill, and add a row to the table above. Never copy content from `example-lsn-longform` or `example-lsn-shorts` into a new format; if a rule applies to both, it belongs here.

## 2. Standing rules

Everything runs on one editing Mac (Premiere Pro 2026, bundle `com.adobe.PremierePro.26`). Footage and projects live on a shared drive, `<DRIVE>/` below. Claude's own outputs (log, playbook, script drafts) go in `Claude outputs/`.

- **Never modify past jobs, templates or hero assets.** They are read-only. To learn from an old project, open a copy or unzip the `.prproj` (gzip XML) and parse it. In the first session the creator said: "Work read-only first and never touch my real projects." Leave footage where the creator put it unless they say otherwise.
- **Never save over a project a human editor may also have open.** If Save pops "Project Modified – the file has been modified since last save", Cancel it (native modal: needs full-screen control) and `save_project_as` to a new name (the creator: "Save as a new project and just add 'shorts' to the name").
- Do the read-only handshake before any edit: `verify_premiere_connection`, `get_premiere_state`. Confirm the project path and the active sequence.
- **Save after every step**, and say so when a step is done and saved. The creator switches projects or models at those points ("tell me when you're done with this step and you've hit save").
- Don't keep a reference project open once its structure is captured; the creator closes it.
- Scratch files go in `<job>/Projects/_claude-work/`. `rm` isn't allowed on the drive, so tell the creator it can be deleted at the end.
- Report briefly: what changed, where it is on the timeline (timecode), that it's saved, and anything that still needs a human listen.

## 3. Where things live (shared)

| What | Path (under `<DRIVE>/`) |
|---|---|
| Edit log | `Claude outputs/Edit-Log.md` |
| Creator Edit Playbook (Jonah Blake, your saved copy) | `Claude outputs/creator-edit-playbook.md` (section 10 has the parts that apply) |
| Premiere bridge install kit | `Adobe Video Skill Kit/` |
| Blank format-skill template | `Claude outputs/format-skill-TEMPLATE.md` |

Format-specific paths (job folders, hero assets, bins) live in the format skills.

## 4. Review notes and delivery (Frame.io)

**Frame.io notes come back as markers.** The creator's review comments arrive on the timeline as markers named after the reviewer (color 5). At the start of any revision pass, `list_markers`, and treat every one as a to-do. Green (color 0) markers are chapters; ignore unnamed ones. Check the marker times against the current cut length: they were written against a specific upload, and a lift applied since shifts everything after it.

**Learning from a finished timeline** (how the long-form finish checklist was built):
1. `get_full_sequence_info` one track at a time (`track_type` + `track_index`, `clip_limit` 200); big results are saved to a file, so parse them with a short python script.
2. `get_effect_properties` Motion on a few representative clips per layout.
3. `export_frame` at 20–25 beats into `<job>/Projects/_claude-work/review/` (`mkdir -p` first), build one contact sheet on the Mac with PIL (composite over black), stage that one jpg and look at it.
4. Timeline screenshots: drag the bottom scrollbar handle to scroll, its end handle to zoom, then `computer_app_zoom` on the track area.
5. Log every difference, then fold.

**Export straight to Frame.io** (Premiere 26.5):
- Export mode has **no** Frame.io destination, and Window › Frame.io is greyed out while in Export mode. Go to **Edit** mode, then `computer_app_menu` `["Window","Frame.io","Frame.io V4"]`.
- In the panel's **Sequences** tab, click the purple **+** next to Share. The "Export to Frame.io" dialog has: Name, Upload Location (your project), Range, Preset (High Quality 1080p HD, H.264 20 Mbps), Upload Markers as Comments (on), Save a Local Copy (off).
- **Range defaults to "Sequence In / Out"** and showed 00:00:00:00. Switch it to **Entire Sequence** and check the footer duration matches the cut before clicking Export.
- Premiere encodes itself (not Media Encoder) and shows "Encoding" then the upload in the panel. The asset then appears in the Browse tab under your project.
- To see live notes: Sequences tab → the linked asset → its comment list; clicking a comment jumps the playhead.
- **Reading the real notes.** Frame.io → timeline marker sync is unreliable across several sequences (one short's sequence showed another short's notes), and Gmail digests carry no comment text. Read notes in the panel: Browse → double-click the asset → "All comments". Comment timecodes are source TC; subtract the clip's start TC shown in the player. Notes left from a second account arrive as markers with that account's name; treat them the same way.
- **Revision rounds (version stacks).** With the revised sequence active, the Sequences tab shows "Current Sequence Asset"; its **+** opens Export to Frame.io with **Add to Version Stack** on, so the upload lands as v2 on the same asset and keeps the comment thread. Turn **off** "Upload Markers as Comments" on revisions or the old note markers come back as duplicate comments. Loop per sequence: `set_active_sequence` (bridge) → wait for the panel to switch → + → check name and duration → Export. Four shorts took ~12 min.
- **Batch uploads** (first delivery): the + always exports the ACTIVE sequence, so loop `set_active_sequence` → + → Export; the dialog remembers Range and Preset, and the panel queues them (badge count on the Sequences tab). Uploads run ~3 at a time and the last few % sit for minutes. Pick the preset by frame shape: 16:9 → High Quality 1080p HD; anything else → **Match Source - Adaptive High Bitrate** (15 Mbps; the 1080p preset letterboxes, "Medium" is only 2 Mbps). Share: Frame.io web → project → select all → Create Share Link.

## 5. Finding b-roll, photos and music

**Internet Archive (free, no API key)**: 17M+ films and 14M+ audio recordings.
- Collections worth searching:
  - `prelinger`: about 10,000 public-domain stock films. Good for vintage lab, hospital, "future of medicine" and science-reel b-roll.
  - `78rpm`: 187,000 old records, for period music under comedic beats.
  - `etree`: 300,000+ live concert recordings.
- Search: `https://archive.org/advancedsearch.php?q=collection:prelinger+AND+(laboratory)&fl[]=identifier,title,year&rows=50&output=json`
- File list: `https://archive.org/metadata/<identifier>`. Download: `https://archive.org/download/<identifier>/<file>`. Prefer the `.mp4` derivative.
- Docs: https://archive.org/developers/
- Download on the Mac with `device_bash` + curl into `<job>/Assets/Footage/Research B-roll/`, if archive.org is on the egress allowlist. Otherwise use Chrome.
- **Check the rights line** (`licenseurl` / `rights` in the metadata) on every item before it goes in a monetized video. "Public domain" on archive.org is only as good as the uploader.

**Stills from a source's own video** (when a script beat needs a picture, not a screenshot of words):
- The sandbox and Mac proxies may block the media host. Open the `.mp4` URL directly in Chrome; it's then same-origin, so a canvas isn't tainted.
- In `javascript_tool`, seek a `<video>` to each time, `drawImage` onto a 1920×1080 canvas, `toBlob`, and click an `<a download>`. The files land in **~/Downloads**.
- Build a thumbnail contact sheet first and pick frames with no on-screen captions.
- When the creator asks for photos, just hand over the files. They drop them in themselves. Don't build `.command` scripts or drive Finder for it.

## 6. Animation Composer (Mister Horse) transitions

AC has no scripting API, so use **computer use** on its panel. The panel is a tab in the top-middle group, next to Effect Controls / Effects / Properties.

- **Browse**: Transitions › Camera › Zoom holds **Zoom In** and **Zoom Out**. Camera also has Blurs & Fades, Pan, Rotate, Shake, Zoom & Rotate. Other transition groups are Glitch, Light Leaks and Shapes. Click a folder name to load its previews on the right, and scroll the preview pane to find the preset.
- **Never type into the AC search box in background mode.** The text doesn't land in the field and goes to Premiere as shortcuts instead (Z, O, M…). Browse the folders.
- **Apply**:
  1. `set_playhead_position` exactly on the cut. Use the full-precision edit time from `get_full_sequence_info`.
  2. `deselect_all_clips`.
  3. **Double-click** the preset thumbnail.

  AC puts "Graphic" adjustment clips on the first free tracks above and adds a track if needed (e.g. V3 + a new V4). That's 2 clips per track, straddling the cut:
  - Zoom In: −0.25 s / +0.50 s.
  - Zoom Out: −0.25 s / +0.71 s.

  "Add sound" (bottom of the panel) is off by default. Leave it off unless asked.
- **Remove** a transition: run `remove_from_timeline` (ripple false, include_linked false) on each of its Graphic clips (4 for one transition on V3+V4).
- **After applying**, Premiere's main window can drop out of `computer_app_list_windows`. Only an off-Space "Save Project" / "Import Files" window shows. Fix it with `computer_app_bring_to_current_space` on that window, then list windows again.
- **Verify**: export one frame on each side of the cut and check the zoom-blur renders. Each AC graphic carries a hidden "If the transition isn't working, visit misterhorse.com/help/pcadj" text. If that text shows in a frame, the transition didn't render.
- In project XML, AC transitions show up as "Mister Horse Adjustment" graphics carrying Transform / Mirror / Directional Blur effects. AC's whoosh SFX only goes in if asked.

## 7. Bridge gotchas (leancoderkavy premiere-pro-mcp, CEP)

- Pass `expected_project_path` and `expected_cep_target {"backend":"cep","sequence_id":…}` on **every** call. Also pass `sequence_id` where the tool takes one. The active sequence can silently flip (e.g. to Assembly).
- After any failed or unknown result, run `inspect_operation_recovery`. Its output is large, so read the saved file with jq. Then `acknowledge_operation_recovery`, re-activate the sequence, and only then retry.
- Insert times must be exact frame multiples (frame = 1001/24000 s at 23.976). Use full precision.
- Inserting on V2 shifts V1/A1 but **not** later V2 clips. For V2 overlays after V1 is built, use `overwrite_clip`.
- `undo` does not revert the bridge's DOM-level shifts. Don't rely on undo to repair inserts.
- `export_sequence` can outlive the 60 s reply window. Poll the file on disk.
- `export_frame` needs its output folder to exist. A failed call leaves `HOST_RECOVERY_REQUIRED`; inspect + acknowledge, then retry.
- `remove_from_timeline` returns `undoSteps` / `undoStackIndex`. Note them in case a reversal is needed.
- `get_full_sequence_info` pages at about 80 clips per 40k-character page. Walk `clip_offset` (0, 79, 163…) and pass `track_type` + `track_index` to page one track at a time. Oversized results are saved to a file; parse them with python rather than reading the JSON.
- `split_clip` is per track and leaves linked audio alone. Split V1 and A1 separately at the same time. Likewise `move_clip`: video and audio move separately.
- `ripple_remove` and the ripple tools refuse 23.976 fps. To shorten a sequence: `set_clip_duration end_seconds` on the clip (music needs `keyframe_policy preserve`), then `move_clip` every later clip left by the same frame-exact delta.
- Keyframed Motion ignores `set_clip_properties_batch`: `remove_keyframe_range` Scale AND Position (end ≤ clip length) first, then set.
- Deleting or moving clips can leave an orphan audio transition past the last clip, which extends the sequence with black and has no delete tool → extend the last clips to cover it (check the tail is silent).
- Masks are invisible to the bridge. To delete one: open the sequence in the timeline, `set_clip_selection` the clip, click the mask name under Opacity in Effect Controls, press delete (a background keystroke works), zoom to confirm.
- Compact listing: `get_sequence_structure` (video). Review loop: `export_sequence_review_frames` (2–24 frames, dir must exist) + a PIL contact sheet. Check host centring with a Haar face detect on the frames (container OpenCV; the Mac VM's cv2 has no CascadeClassifier).
- Any import (`freeze_frame`, AC apply) or Save can make the main window vanish from `computer_app_list_windows`; see section 8.
- **Never fire parallel mutating calls.** One validation error (e.g. `remove_keyframe_range` with `end_seconds` past the clip) poisons the batch with `HOST_RECOVERY_REQUIRED`. Sequential only; read-only calls can run in parallel.
- `set_clip_properties_batch` reports "verified" on a keyframed property while changing nothing. Check `get_keyframes` first and remove the range.
- `trim_clip` refuses nested-sequence sources (no ffprobe duration) and trips HOST_RECOVERY; use `set_clip_duration`.
- `device_bash` is a Linux VM, not macOS: no `sips`; use PIL or ffprobe for image dims.

## 8. Computer-use gotchas (Premiere)

- Grant: `com.adobe.PremierePro.26` in background mode. Get the main window id from `computer_app_list_windows` (it changes per launch). Screenshot frame is 1456×819.
- Dropdowns inside Premiere's own dialogs (e.g. the Frame.io export Range menu) open fine with a background `computer_app_click`; only Track Mixer slots and context menus need full control.
- Premiere's AX summary is empty. Clicks go as raw input, so verify every click with a screenshot or a bridge readback.
- Access expires after 30 idle minutes: `computer_resolve_access` then `computer_request_access` again.
- Stray keystrokes become shortcuts. If a tool change, marker or in/out may have happened, check with `list_markers` and `get_sequence_in_out_points`. Restore the Selection tool from the top of the left toolbar.
- Prefer the bridge for anything it can do. Use computer use only for panels with no API: AC, Essential Sound, Audio Track Mixer inserts, Transcribe, Enhance Speech.
- **Two displays.** Premiere's main window is on **"<main display name>"**; the Program monitor is on **"<second display name>"**, which `computer_screenshot` picks by default. Run `computer_switch_display "<main display name>"` before display-scope work.
- **Main window gone from the list**: only an off-Space "Save Project" / "Import Files" window shows. `computer_app_bring_to_current_space` on that window brings the main window back (it may time out, but the main window is back on the next `list_windows`). A bridge save can trigger this too.
- Native modals ("Project Modified", Save dialogs) ignore background clicks; they need full-screen control.
- **Essential Sound** (background mode works): with the clips selected, the Edit tab shows "Multiple Clips Selected", the Dialogue tag, and a Loudness section with Auto-Match. Click Auto-Match, wait ~15 s, screenshot to read the confirmation.
- **Audio Track Mixer inserts** need full-screen control (`computer_request_full_control`), because the slots are pop-up menus:
  1. Click the ">" at the mixer's top-left to show effects and sends.
  2. Click slot 1 of a track, then its ▾ → Amplitude and Compression › Hard Limiter.
  3. **Double-click** the slot name. The "Track Fx Editor" window opens. Click the blue Maximum Amplitude value, type `-8`, Return. Close the editor with its red button.
  4. Repeat for A2, A3 and Mix. Single-clicking the knob value in the mixer does not open an edit field.

## 9. Creator preferences (general; the format skills add their own)

Fill this in from the creator's own words and keep it current through the log. Examples of the kind of line that belongs here:

- Save after every step and say so. Report briefly: what changed, where (timecode), saved, what still needs a human listen.
- What "done" means for them (e.g. 80–90 % of the way, a human editor finishes, notes come back in Frame.io).
- How they want files handed over, and whether they review on a phone (skill cards don't render there).
- Hard lines on their likeness: e.g. no AI-generated images of the creator, thumbnail faces only from that day's recording, no face grading.
- Audio pass they expect on every job.
- What timeline marker colours mean to them.

## 10. Borrowed craft rules

This structure pairs well with Jonah Blake's **Creator Edit Playbook** (his X post: https://x.com/jonahblake/status/2114742030434013281). It is his work, so it is not reproduced here; save your own copy next to the log and point this section at it. The principles we adopted from it, in one line each, so the format recipes can reference them:

- Cut without losing a word: only dead air comes out; re-transcribe the cut and diff it against the source.
- A fact-check file per video (claim, timestamp, verdict, source, on-screen label) before any graphics; neutral labels, no "WRONG" stamps.
- News-show graphics: something changes every 5–8 s; no graphic under ~0.5 s; no flicker; the last stat stays readable before the end card.
- Sponsor reads carry an "Ad" chip and a disclosure chip from the first frame.
- Shorts: frame 1 must already work; captions 2–3 words on the seam; no music on talking shorts unless asked.
- Audio −14 LUFS, true peak ≤ −1 dBTP; measure with `ffmpeg -af ebur128`.
- Thumbnails only from that day's footage, never graded or AI-generated.
- QA on every final (flash detector, loudness, sync, decode, re-transcription diff) and an independent reviewer that didn't build the cut.
- Phone previews under 30 MB go out first.

Add your own borrowed sources the same way: credit, link, one-line principles, pointer to your saved copy.
