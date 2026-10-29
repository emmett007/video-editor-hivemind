---
name: example-lsn-longform
description: Edit a Longevity Science News 16:9 episode in Premiere: new J-job setup from the template, A-roll rough cut from the transcript, research clips, screenshots in the news-anchor layout with punch-ins, LSN intro/outro GFX, music bed, markers, audio pass, finish-quality checklist. Loads video-editor-hivemind first.
---

# LSN long form (16:9 episodes)

> **Worked example.** This is a real format skill from Longevity Science News, kept intact (with drive paths and names generalised) so you can see how much lives in a format file versus the Hivemind. Build yours from `format-template`, not by editing this.

**Load `video-editor-hivemind` first** and read the edit log it points to; this file adds only what is specific to LSN episodes. For shorts cut from a finished episode, use `example-lsn-shorts`.

## 1. Where things live

| What | Path (under `<DRIVE>/<channel>/`) |
|---|---|
| Jobs | `J## Title` / `J##_ Title` (naming varies), e.g. `J78 Rentosertib`, `J79_ ER-100 First Results` |
| Folder template | `J00_ORG-TEMPLATE/`: `Assets/{Scripts, Images, Footage/{Research B-roll, A7iii, EOSR, RX100viii, gopro, iphone, Drone, 360}}`, `Exports/`, `Projects/J00_ORG-TEMPLATE.prproj` |
| Intro sting | `00_Hero Assets/LSN Opening GFX.mp4`: 2.169 s, has its own audio |
| End card, logos | `00_Hero Assets/LSN End Card 16x9.png`, `Logo_LSN_*.png`, `Awed Media Logo.png` |
| Intro music bed | `00 GFX Template/Projects/Intro & Outro Template/Copied_Template/Audiio_Lucha_Demons_Demons_Inst.wav` |
| Job GFX | `<job>/Assets/GFX/`: animated tweets/quotes/graphs, `Subscribe graphic.mov`, the end-freeze PNG |
| AI animations | `<job>/Assets/Agent Opus/Outputs/` |
| Scripts | job `Assets/Scripts/J##_…_SCRIPT.md` + `_TELEPROMPTER.md`; Claude's script drafts are in `Claude outputs/` |

- **A-roll** is a camera `.mp4` plus a separate recorder `.WAV` with the same base name (e.g. `LSN_J79_ER-100_&_SL-100.mp4/.WAV`) in `Assets/Footage/`. They must be synced. In J79 the WAV led by **3.4618 s** (assembly time = WAV time − 3.4618). Cross-correlate the two audio tracks to find the offset for each new job.
- The camera mp4 carries **3 extra silent audio channels**. Emmett wants the empty tracks deleted and the WAV moved up to A2.
- **Screenshots**: `Assets/Images/NN - Source, description.png`, numbered in script order. Sub-shots get a letter (`22b`, `41a`). The script's editor notes say where each one goes.
- **Research clips**: `Assets/Footage/Research B-roll/`, numbered `#01…`. J78 used `Assets/B-Roll Clips`, `Assets/AO`, `Assets/GFX` instead. For J79 Emmett chose Images + Research B-roll.
- **Project bins** (template): AO, Comps, Footage, GFX, Images, Music, Prev Proj, Sequences, SFX. Animation Composer adds its own `Animation Composer` bin the first time it's used, and writes `Projects/Animation Composer/Packs/`.

## 2. New job setup (J79 is the reference)

1. Copy `J00_ORG-TEMPLATE.prproj` into the job's `Projects/`. Open it, then Save As `J##_<Title>.prproj` (J78: `J78_Rentosertib.prproj`; J79: `J79_ER-100 First Results.prproj`). Keep the copied template untouched.
2. Import into the matching bins:
   - A-roll mp4 + WAV → Footage
   - screenshots → Images
   - research clips → Footage (or a B-roll sub-bin)
   - `LSN Opening GFX.mp4` → GFX
   - intro music → Music
3. Build an **Assembly** sequence from the camera mp4: V1 camera video, A1 camera audio (muted), A2 the synced WAV. Delete the empty extra audio tracks.
4. Build the main sequence named exactly like the project (`J79_ER-100 First Results`): **3840×2160, 23.976 fps**, 48 kHz stereo. Short reordered pieces become their own sequences named `J## piece - <what>`. All sequences go in the Sequences bin.

## 3. Rough cut recipe

**Transcribe**:
- Stage a 16 kHz mono MP3 of the WAV into the cloud container. Staging a big WAV off a network drive times out, so compress on the device with ffmpeg first.
- Run sherpa-onnx with `sherpa-onnx-nemo-parakeet-tdt-0.6b-v2-int8` plus `silero_vad.onnx`.
- That gives segments and word times. The VAD can drop segments, so check coverage against the script.

**Pick takes**: compare against the TELEPROMPTER script. Keep the **last complete** take of each line. Drop false starts, restarts and off-script chatter. Also drop Emmett's **reactions to clips** ("cut this reaction from me and just start with 'this news represents…'") and any scene that doesn't land. When in doubt, cut.

**Cut points**:
- At segment level: start −0.12 s, end +0.18–0.25 s.
- At word level: start −0.10 s, end +0.05 s.
- Snap to the quietest 10 ms frame nearby. Never bisect a word.
- **Pacing target: the finished J79 V1 is 148 pieces with a median length of 3.5 s** (29:08 of Assembly → 16:47). My rough cut kept sentence-length runs; the editor cut on breaths. Tighten every pause >0.5 s inside a run to ~0.25 s, and pauses between sentences to ~0.5 s.
- Put a 2–4-frame Constant Power dissolve on A1 at hard jump cuts, and into every research clip.

**Build V1/A1**:
- Nest the Assembly into the main sequence.
- Remove everything not kept with `ripple_remove_timeline_ranges`. Run `dry_run` first, then apply with the token. Use at most 50 ranges per call, all in pre-edit coordinates, batched from the **end backward**.
- For reorders, build a piece sequence and insert it.

**V2/A2, research clips**:
- Insert with `apply_edit_plan` (`insert_clip`) from the end backward. The scripted part starts 3 s into each clip, with 3 s handles.
- Then ripple-remove the handles.
- Scale **200 %**, because the clips are 1080p in a UHD sequence.

**LSN Opening GFX, intro**:
- Goes on V2/A2 at the **Intro** chapter, with a gap on V1 under it (the A-roll stops for the sting).
- In J79 it plays right after the cold open, at 1:18:17.
- AC **Zoom Out** at its start, AC **Zoom In** at its end (Hivemind section 6).

**LSN Opening GFX, end of video** (Emmett's spec, 2026-10-21):
1. `overwrite_clip` the GFX on V2/A2 at the last A-roll frame, scale 200 %.
2. AC **Zoom Out** at its start. **No** Zoom In after it.
3. `freeze_frame` at (GFX end − 1 frame), `duration_seconds` 4. It writes a 4K PNG, so give it `<job>/Assets/GFX/LSN Opening GFX - last frame.png`. The still lands in the project root: `move_items_to_bin` → GFX.
4. `overwrite_clip` the still on V2 at the GFX end. 4 s snaps to 96 frames (4.004 s).
5. `add_transition_to_clip` **Cross Dissolve**, position `end`, 1 s on the still. Nothing is under it, so it fades to black. Emmett didn't set the fade length; 1 s was accepted.

**V3, screenshots (news-anchor layout)**:
- `overwrite_clip` at the spoken beat, 0.10 s before the cue word.
- **Duration = the whole beat it illustrates.** Read the transcript words and keep the still up until he stops talking about that thing, or the next graphic starts. Never leave a still at Premiere's default 5 s. The J79 editor's stills ran 3–20 s; Emmett's notes on my cut were "still talking about the last GFX and it's gone" and "the previous tweet can show through this whole section". Don't run into a V2 clip.
- **Match content, not just the note number.** Before placing, check that the still actually shows what he's saying at that beat ("wrong image: optic nerve, not OSK molecules", "this is a tweet from a whole different guy").
- **Show the primary evidence.** When results are first stated, the company's own press release / X post / data table must be on screen.
- **Name lower-third** on the first appearance of every person: a Text graphic on V3 with name + title, over their headshot (`Assets/Images/<Name>.jpg`). Pull a headshot for every scientist/CEO the script names.
- **Highlight the words he reads**: a red underline from the Mister Horse MOGRT "Line 2 - Left To Right" (3840×2160, Motion Graphics Templates panel), `import_mogrt` on V4 over a flat still; animated quote/tweet cards carry a **yellow highlighter** on the key phrase instead.
- Key beats (the headline tweet, the main quote, the main graph) get an **animated** version instead of a flat still: Agent Opus / AE renders in `Assets/GFX/` (`sinclair tweet animated.mov`, `median lifespan graph.mp4`…), played at 1.5–2× so the build finishes inside the beat. Article quotes become quote GFX, not screenshots.
- **Never centre a graphic over the host's face.** Two layouts, picked by the still's shape (seen in the finished J79):
  - **Tall / portrait** (tweets, infographics, phone screenshots, headshots): **left column**, over Emmett's right shoulder. Fit inside a **1430 × 1700 px box centred at (815, 1080)**; `scale = min(1430/w, 1700/h, 3.0) × 100`. The A-roll is **punched in** under it (next block).
  - **Wide / landscape** (headlines, article quote cards, Google AI-overview, data tables): **lower-third band**, centred, Position **(1920, 1690)** = (0.5, 0.78), scaled to ~75 % of frame width. The A-roll stays at **100 %** with Emmett's head centred. No punch-in.
  - Over a research clip, a still goes to the **top** of frame so the clip's faces stay clear.
  - Set with `set_clip_properties_batch` (16 clips per call, `position_x`/`position_y` in sequence pixels).
- **Headshot card**: portrait photo about 25 % of frame width in the left column, caption **under** the photo: white text with drop shadow, centred, "Name - Title" (or Name / Title on two lines). J79: "George Church - Geneticist", "Shinya Yamanaka / Nobel Laureate", "Christopher Petty / PhD Student - Harvard", "Vadim Gladyshev - Biologist", "Aubrey de Grey - Gerontologist".
- **Host PiP over a full-frame animation**: when an Agent Opus clip runs full frame on V2, the A-roll piece nest goes on V3 at Motion scale **59 %**, Position **(0.146, 0.338)**, with an **ellipse Opacity mask** around Emmett's head → a round talking-head bubble top-left, about 18 % of frame width. That's how the "J## piece" nests are used.
- Emmett corrected a centred 75 %-wide first pass on 2026-10-21.

**A-roll punch-in while a graphic is up** (Emmett's spec, 2026-10-21):
- Only where a **left-column** graphic is on screen. The rest of the A-roll, and anything under a lower-third graphic, stays at 100 %.
- Scale **130 %** (Emmett's range: 125–140), Position **(2460, 1330)**. That moves Emmett's head right so the right side has less dead space, and the +250 px Y keeps headroom. Emmett's face lands at about 0.65–0.70 of frame width.
- How: for every graphic start/end that is more than 6 frames from an existing V1 cut, `split_clip` V1 **and** A1 at that time (it's per track; the left piece keeps its node id, the right piece gets a new one). If a boundary is within 6 frames of a cut, snap to the cut instead so there are no 1–2-frame slivers. Then re-read V1 and `set_clip_properties_batch` every piece that sits inside a graphic range.
- Results in a hard punch-in on the frame the graphic appears, which reads as deliberate.
- If two graphics are **<4 s apart, hold the punch-in through the gap**. A 100 % clip between two punched-in clips reads as a "weird zoom" (Emmett, J79 at 15:36).
- The J79 editor kept these numbers (130 % at (0.64, 0.616); stills at x = 0.212), so they are house style now.

**A3/A4, music**:
- The intro bed runs from 0 to the first content chapter after the intro, at **−38 dB**.
- −20 dB masked the voice, which the transcription check caught. Emmett on the J79 cut: "music is too loud on intro", "music crescendo happens too early and is distractingly loud".
- Time the bed so its **crescendo lands on the LSN Opening GFX**, not before it. Fade in 0.7 s at 0, fade out ~3 s under the sting.

**Outro** (Emmett's J79 notes): before the end sting, show the **members-only content GFX**, then the **Subscribe graphic** (`Assets/GFX/Subscribe graphic.mov`), with the headshot of whoever he name-checks last (Aubrey de Grey in J79).

**Sponsor read**: a separate recording (e.g. `LSN_Hume_New_Read.mp4`) on V1 at the red marker; add Lumetri Color to match the A-roll.

**Markers**, with `add_markers_batch`:
- **Green** (color 0) chapter markers named with the chapter title, comment `Chapter`. J79: Cold Open, Intro, then one per script section, ending with Conclusion.
- A **red** (color 1) placeholder for the sponsor read, e.g. `SPONSOR READ - <sponsor>, code <CODE>`, comment "insert here", at the script's sponsor slot.

**Audio pass** (Emmett's spec, 2026-10-21):
1. Select all A-roll clips on V1/A1 (`select_clips_in_range` or Emmett does it). Nested-sequence clips are fine.
2. Essential Sound (a tab next to the Audio Track Mixer): tag **Dialogue** if not already, then Loudness › **Auto-Match**. Premiere's target is −23 LUFS. The panel says "Auto-Matched to target loudness of -23.00 LUFS" when done. A1 only.
3. Audio Track Mixer: a **Hard Limiter** at **−8 dB** Maximum Amplitude in slot 1 of **every** track with audio, **including the Mix**. Needs full-screen control, see Hivemind section 8.

**Agent Opus / AI animations**: keep the newest generation on the track and leave older takes **disabled** as alternates. Speed-change (0.8–2×) to fit the beat. If a beat has a real image (a magazine cover, the company's data), Emmett prefers that over an animation.

**Verify before reporting**:
- Export the cut's audio and transcribe it again. Read it against the script and correlate it with the source.
- Spot-check frames with `export_frame` to `_claude-work/` and look at a contact sheet.
- Run `check_offline_media`, then save.

## 4. LSN preferences (on top of the Hivemind list)

- Rough cut means everything placed: A-roll cut, intro GFX, research clips, screenshots, music bed, chapter markers, sponsor placeholder.
- Keep the music bed low under the cold open. The voice must stay clear.
- The camera mp4's empty extra audio tracks get deleted and the WAV moved up to A2.
- News-anchor layout: graphic left, host right. While a graphic is up, punch the A-roll in to 125–140 % and move Emmett's head right. Not elsewhere.
- The video ends on the LSN graphic: Zoom Out in, freeze the last frame 4 s, fade out.
- **AC intro convention**: Zoom OUT into the Opening GFX at its start, Zoom IN out of it at its end. Emmett corrected the reverse on 2026-10-21.

## 5. Finish-quality checklist (what the human editor had to fix on J79)

Run this before calling a cut done. Each line came from diffing the editor's finished J79 sequence against the Claude cut, from 22 exported frames of it, and from Emmett's Frame.io notes on it.

1. **Pacing**: V1 pieces average ~4 s; no pause >0.5 s inside a run. Reactions to clips and weak scenes are cut.
2. **Every graphic stays up for its whole beat**, and ends on a topic change or the next graphic, never on the 5 s default.
3. **Every still matches the words** being said at that moment. The primary source (press release, data, the actual tweet) is shown when results are stated.
4. **Every named person** gets a headshot card with the name/title caption under it on first appearance.
5. **Key beats are animated** (tweet, quote, graph) and dense screenshots get a Line underline on the words he reads.
6. **Layout by shape**: tall stills left + punch-in; wide stills lower-third + 100 %; full-frame animations get the round host PiP top-left.
7. **Punch-in holds** across gaps <4 s between graphics.
8. **Music** ducks under every word; the crescendo hits on the LSN sting; fades on both ends.
9. **Audio dissolves** (2–4 frames) on hard A1 jump cuts and into research clips.
10. **Sponsor read** colour-matched with Lumetri.
11. **Outro**: members GFX → Subscribe graphic → LSN sting (Zoom Out in, 4 s freeze, fade).
12. Old AO takes left disabled, not deleted; new generations used.
13. Read every `Emmett` marker and close it out.
