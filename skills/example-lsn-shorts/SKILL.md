---
name: example-lsn-shorts
description: Cut 30–60 s vertical (9:16, 2160×3840) shorts from a finished Longevity Science News episode in Premiere: pick hook-first moments from the transcript, build each as its own sequence in a "<project> shorts" copy, reframe host and graphics with layout v2 (speaker on top, graphics filling the bottom, no black), export to Frame.io. Loads video-editor-hivemind first.
---

# LSN shorts (9:16 from the long form)

> **Worked example.** This is a real format skill from Longevity Science News, kept intact (with drive paths and names generalised) so you can see how much lives in a format file versus the Hivemind. Build yours from `format-template`, not by editing this.

**Load `video-editor-hivemind` first** and read the edit log it points to; this file adds only what is specific to vertical shorts. Emmett's spec: each short opens on a hook sentence whose first ~10 words give the whole premise, then delivers it; Emmett's face and the graphics are reframed for vertical; each short is its own named sequence so the original stays untouched; everything goes to Frame.io for notes.

## 1. Recipe and layout

Built on J79 (10 shorts, 2026-10-24) and revised from Emmett's Frame.io notes (2026-10-28). Each short is its own sequence; the long-form sequence is never changed.

**Project**: build in a copy named `<project> shorts.prproj` (`save_project_as`; Emmett: "Save as a new project and just add 'shorts' to the name"). Never save over the editor's project (Hivemind section 2).

**Picking moments**: map the WAV transcript onto the FINAL timeline through the V1/V3 Assembly clip in-points (assembly = WAV − offset; a word belongs to the clip whose [in, out) holds its start). Choose 30–60 s ranges whose **first sentence states the whole premise in ~10 words** ("People are walking around right now with age reversal genes inside their eyeballs"). Start and end on existing clip boundaries. Cutaways carry **only the quote line** (cut lead-ins like "we've done it in a mouse, but suddenly"). End **≥0.5 s after the last word**.

**Build recipe** (one short):
1. `set_sequence_in_out_points` on the long form → `create_subsequence` (`ignore_track_targeting` true: keeps all tracks, effects, masks, music, transitions, markers) → `rename_project_item` (the sub is always `<seq>_Sub_01`) → `set_active_sequence`.
2. `set_sequence_resolution` **2160×3840 first**, then `get_sequence_structure` (video) to list the clips, then `set_clip_properties_batch` (≤16 items, positions in the new pixel space).
3. To shorten (trim a cutaway to the quote line, add tail after the last word): `set_clip_duration` + `move_clip` every later clip left by the same delta, V and A separately (Hivemind section 7; ripple refuses 23.976).
4. Review with `export_sequence_review_frames` + a contact sheet; verify host centring with a face detect (Hivemind section 7): **face x ≈ 1080 ±60**.

**Layout v2 (current; Emmett: "there should never be any black empty space in these shorts")**, on 2160×3840:
- Host on TOP, graphics on the BOTTOM, always full-bleed. A-roll **178 % at Position (1055, 1920)** with or without a graphic (face lands ~y 850). Not (465, 1920): that put Emmett's face left of centre ("ALL THE CLIPS of our speaker are slightly off to the left").
- Graphics fill the 1920–3840 band, bottom-anchored and centred at x 1080, scaled past full width if needed: tall tweet 905×974 → 195 % at (1080, 2880); wide tweet 917×504 → 230 % at (1080, 2880); 1080² animated graph → Crop 7/7/9.5/12 % + 225 % at (1080, 2911); wide stills → width 2160; tall stills → height ~1850, centred y 2880.
- "Crop it so only the image is visible": Crop effect to the photo's bbox (find it with PIL), then scale past full width and push flush to the frame bottom. A blend-mode duplicate nest on V4 above the still hides it → crop its bottom so it stops above the graphic.
- Agent Opus b-roll UNDER the A-roll: A-roll 178 % (1055, 1920) + Crop Bottom 50 % (speaker fills the top half); AO cover-scaled to the 2160×1920 bottom band at (1080, 2880): 1920×1080 → 177.8 %, 1344×756 → 254.5 %. ffprobe the AO pixel size first.
- Research b-roll with no A-roll under it: single shot → full-bleed 355.6 % centred on the speaker's face; two-shot with a graphic → split at the shot change (ffmpeg scene detect), 268 % framed on the speaker covering y 0–2894, graphic bottom-anchored.
- The long form's host PiP nests (Motion 59 %, ellipse mask) keep their mask after rescaling. **Delete the ellipse masks** (Emmett: no circle bubbles in shorts; he fills the frame) with the mask method in Hivemind section 7, then treat the nest like any A-roll clip.
- **Paired overlays** (Line MOGRT over a still, Nested Sequence 01 tweet+highlight) move with the still's OWN old scale: k = new/old; pos' = still_new + (pos − still_old)·k; scale' = 100·k. Measure a nest's content from a frame first; verify the underline with a zoomed crop.
- The cold-open push-in (100→120 %) is keyframed: remove its Scale and Position keyframes before setting (Hivemind section 7).

(Layout v1, 2026-10-24, superseded: A-roll 125 % at (648, 2750) with a 2000×1400 graphics zone on top centred (1080, 760). Keep for reading old sequences only.)

## 2. Shorts preferences (Emmett's Frame.io notes, 2026-10-28)

- Speaker on top, graphics and b-roll on the bottom, expanded to full width. **Never any black empty space.**
- The speaker is **always centred** (stressed for every short) and fills the frame; no ellipse/circle PiP masks around Emmett.
- Cutaways contain only the quoted line; cut lead-ins.
- Don't clip the last word: end ≥0.5 s after it.
- Frame 1 must already work (Hivemind section 10, Shorts): hook readable, no fade-ins. No music or SFX on talking shorts unless he wants them.

## 3. Delivery

- Export each short from the Frame.io V4 panel with Preset **Match Source - Adaptive High Bitrate**; the batch and version-stack loops are in Hivemind section 4. Emmett's notes come back as `Emmett` / `mkt opus` markers, but marker sync across many sequences is unreliable, so read the comments in the panel.
- When Emmett leaves notes on a short, fix it in the same sequence and re-upload to its version stack.
