# Claude.ai User Preferences — canonical maintenance project

## What this is

This folder holds the canonical version of my Claude.ai User Preferences (`user-preferences.md`). Edits happen here, not in the claude.ai Settings UI. Claude.ai Settings → Profile → User Preferences is the deployment target, updated by manual paste from this file when changes are ready to go live.

## Workflow

1. I describe a change (add rule, sharpen, retire, restructure). Or I paste in a Claude Code handoff prompt produced by a claude.ai chat — per Part 4's in-chat editing flow, chats now format edits as handoff prompts rather than instructing me to find-and-replace manually.
2. You run Part 3D ("Audit Before You Add") from `user-preferences.md` before any new rule lands.
3. You edit `user-preferences.md` directly using file-edit tools.
4. We git commit with a descriptive message naming the rule and the change.
5. I review the diff.
6. When ready, I copy the full file contents and paste into claude.ai Settings → Profile → User Preferences, then save.
7. Chats reflect the updated preferences on their next user message after the save: active chats refresh mid-chat, not only at chat start (corrected lifecycle model, lesson-3-anomaly.md). A fresh chat is still the cleanest way to confirm a deploy and avoid context saturation, but is no longer required for the change to take effect.

## Rules that apply

All rules in `user-preferences.md` apply when editing the file. Specifically:

- Part 3 (reactive drift correction)
- Part 3B (proactive drift detection)
- Part 3C (audit on request)
- Part 3D (audit before you add)
- Part 3E (tiebreaker for same-layer conflicts)
- Part 3F (continuous preferences audit)
- Part 4 → Local artifact editing workflow

## What NOT to do

- Don't paste edits directly into claude.ai Settings without going through the local file first.
- Don't make multi-section edits without writing interaction notes (Part 3D step 5).
- Don't add a new rule without running Part 3D first — sharpening existing rules is the default; adding is the exception.

## Project knowledge note

The broader curriculum work (lessons, tests, audit findings) lives in a separate Claude.ai project called "Mastering Claude Customization." This folder is for the preferences doc itself, not the curriculum work.