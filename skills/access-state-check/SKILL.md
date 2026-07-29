---
name: access-state-check
description: Use before writing any claim about what Claude can or cannot read, reach, see, or access in this workspace, and before telling the user to fetch, paste, upload, or run a command to supply something. Triggers on phrases Claude is about to write ("I can't read", "I don't have access to", "I'm unable to see", "you'll need to paste", "run this and send me the output") and on user questions about Claude's access ("can you see X", "why can't you read X", "do you have access to"). Also use before naming a file path, directory, or install location as if it exists, and before emitting any path, command, or shell syntax into an artifact that a different surface will execute (a handoff, a script, a paste block) where the executing surface is Windows vs WSL vs a sandbox, or PowerShell vs Bash. Do NOT use for claims about Claude's reasoning, tone, policy limits, or refusals, and do NOT fire on ordinary command use where no cross-surface boundary is crossed; this skill is about environment access and surface mismatch only.
---

# Access-state check

## The rule

A statement about what Claude can or cannot reach in this workspace is a
claim about observable context, not a fact about Claude. It is checked
before it is written, never asserted from a general sense of what Claude
usually has.

Access is never binary. Answer in three states, and name the one that
applies:

| State | Meaning | How to say it |
|---|---|---|
| Live | Read directly, current as of this moment | "I can read this now" |
| Snapshot | Present in context via a connector, project knowledge, or an upload; freshness unknown | "I have a cached snapshot; it may lag the source" |
| Absent | Genuinely not in context and not reachable | "This is not available to me" |

Snapshot is the common state and the one most often mis-stated as
absent. When it applies, say so, name what the snapshot cannot carry,
and derive from what is there rather than sending the user to fetch it.

## Procedure

1. Inventory before concluding. Enumerate what is actually attached:
   connectors, project knowledge files, uploaded files, mounted paths,
   and content already quoted in prior turns. Look; do not recall.
2. Classify the specific thing in question as live, snapshot, or absent.
3. On snapshot, name the boundary. State what the snapshot carries and
   what it does not, so the user can tell which follow-up is real.
   Known boundaries: a GitHub connector serves file contents but not git
   metadata (no git log, rev-parse, branch or working-tree status);
   project knowledge is retrieved by search and may lag the repo; an
   uploaded file is fixed at upload time.
4. Do not send the user for what is already in context. A fetch
   instruction is only valid for the part that is genuinely absent.
   Scope the request to that part.
5. State the access line before the conclusion drawn from it, so a
   skipped check is visible in the response rather than discovered later.

## Surfaces are access claims too

Naming a file path, directory, or install location asserts that it
exists and is reachable from the surface being addressed. The same
applies to any command, syntax, or tool invocation: it carries an
assumption about which surface will execute it. Verify the surface or
label the assumption unverified.

Surfaces routinely confused: the claude.ai sandbox tree versus the
user's own machine; PowerShell versus Bash versus WSL; a Claude Code
path versus a claude.ai install. An artifact authored for the wrong
surface aborts at best and writes somewhere disposable at worst.

Before emitting a path or command, name which surface executes it.

## Handoffs must be self-contained

A payload written for an executor contains everything the executor
needs. An instruction addressed to the user ("paste the block from
above") inside an executor's block is a defect: the executor reads it
as content and has nothing to write.

## Failure this prevents

Asserting an access limit that context contradicts, then apologising
after the user points at the evidence. The apology is not the fix; not
making the claim is.

## Failure this risks

Narrating access state on turns where nobody asked and nothing depends
on it. Fires only when a claim about access is about to be written, a
path is about to be named, or a conclusion turns on either. If nothing
hinges on access, say nothing.
