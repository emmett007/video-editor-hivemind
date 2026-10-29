# Video Editor Hivemind

A skill structure for editing video in Adobe Premiere Pro with Claude (Cowork / Claude Code) that keeps getting better instead of resetting every session.

Built and used on Longevity Science News. Claude does the rough cut (take selection from the transcript, graphics placed on the spoken beat, intro/outro, music, markers, audio pass, Frame.io delivery); a human editor finishes the last 10 %.

## The idea

One **Hivemind** skill holds everything that is true for every video you cut: tool gotchas, delivery, QA, your standing preferences, and a learning loop. One **format** skill per kind of video (a 16:9 episode, a vertical short, an ad) holds only what is different about that format: frame size, layout numbers, pacing, brand assets. Format skills load the Hivemind first, so anything that lands in it is live in every format the next time it runs.

Three habits make it compound:

1. **A living log on your drive.** Skills can only change through a review card, so every lesson (a correction, a tool failure, a workaround, a preference) is appended to a plain markdown log the moment it happens, tagged with the skill it belongs to. Every session starts by reading the log. Periodically the log is folded into the skill files.
2. **A capabilities index in the Hivemind.** Knowing a tool exists is not the same as reaching for it at the right step. Every capability gets a row saying when to use it, and every new one triggers a walk through the format recipes to leave a pointer where it changes a step.
3. **One-way inheritance.** Hivemind → format, never format → format. New formats start from the blank template, not from a copy of an existing one.

## What's here

```
skills/
  video-editor-hivemind/   the shared skill every format loads first
  format-template/         blank format skill to copy
  example-lsn-longform/    a real 16:9 episode skill, paths generalised
  example-lsn-shorts/      a real 9:16 shorts skill, paths generalised
log/
  Edit-Log.md              starter log with the line format
```

## Install in 5 minutes

**1. The skills (copy and paste).** Open any `SKILL.md` in this repo, paste it into Claude and say "save this as a skill"; Claude offers a card to save it. In Claude Code, drop the folders into `.claude/skills/`. Start with `video-editor-hivemind` and `format-template`.

**2. The Premiere bridge (the one real install).** The Hivemind drives Premiere through the open-source [leancoderkavy/premiere-pro-mcp](https://github.com/leancoderkavy/premiere-pro-mcp) (MIT): a local MCP server plus a CEP panel inside Premiere. If you already have it, skip this.

```
npm install -g premiere-pro-mcp
premiere-pro-mcp --install-cep      # quit Premiere first
premiere-pro-mcp --doctor
```

Then add the server to your client. Claude Desktop: Settings › Extensions › Advanced settings › Install Extension (the `.mcpb` bundle from the bridge repo), or add `{"mcpServers":{"premiere-pro":{"command":"premiere-pro-mcp"}}}` to `claude_desktop_config.json`. Claude Code: `/plugin marketplace add leancoderkavy/premiere-pro-mcp` then `/plugin install premiere-pro@premiere-pro-mcp`. Restart Premiere, open Window › Extensions › MCP for Adobe Premiere Pro, start a new Claude session.

The Hivemind checks for the bridge on first run and walks you through this if it's missing, so you can also just paste the skills and ask Claude to edit something.

**3. Panels with no API** (Animation Composer, Essential Sound, Track Mixer, the Frame.io panel) are driven with computer use. Claude asks for app access the first time it needs it.

## Setup, the rest

1. Put `log/Edit-Log.md` on the drive your footage lives on and set its path in `video-editor-hivemind` section 0.
2. Copy `format-template` to a new skill per format you cut. Fill in only what is different about that format. Leave the examples as reading material.
3. Replace every `<DRIVE>`, `<channel>`, `<main display name>` placeholder, and rewrite Hivemind section 9 (preferences) in your own words.

## Credits

The craft rules summarised in Hivemind section 10 come from Jonah Blake's Creator Edit Playbook (https://x.com/jonahblake/status/2114742030434013281). Save your own copy of his post next to your log; it is not reproduced here.

## Licence

MIT for the skill files. Your log, your footage and your preferences stay yours.
