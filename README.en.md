# cross-session-messaging — a skill for AI sessions that talk to each other

**A Claude Code skill for when Claude Code sessions on the same machine (and OpenAI Codex) pass work to each other. It prevents wrong recipients, asking the human twice, and "sent but never read".**

日本語＝[README.md](README.md)

## Install (2 lines)

In Claude Code:

```
/plugin marketplace add Rurimpa/cross-session-messaging
/plugin install cross-session-messaging@cross-session-messaging
```

## What to say after installing

Just ask for something to be passed to another session, as you normally would. For example:

- "Ask `<other session name>` whether the tests pass over there"
- "Have Codex look at this diff"
- "Send this to the other session"

The skill is loaded by phrases like these. Before sending, it checks whether the other side replies on its own, whether a human's approval is being carried, and what one message should contain. To call it directly: `/cross-session-messaging:cross-session-messaging` (a skill installed from a plugin gets the plugin name as a prefix).

## What's inside

- **Which relationship you are in**: a running Claude Code session (replies on its own) vs Codex (a fresh session every call, or a message read only when its turn ends).
- **Carrying a human's approval**: how to hand an approval to another session so the human is not asked twice (scope, the human's exact words, a way to check), and why the receiver still must not simply trust the sender.
- **"Sent" is not "read"**: a message queued to Codex returned exit code 0 without arriving. Check that your marker appears in the other side's transcript.
- **Counting agreement and reading silence**: three voices from one source count as one. No reply is not "no objection".
- **How to write one message**: why you ask, what you ran and what came back, closed questions.

Codex details are in `skills/cross-session-messaging/references/codex.md`. If you do not use Codex, the main skill is enough.

## Notes

- Built from observations of many sessions running at once on one Windows 11 machine, August–October 2026. Codex behavior changes often; re-check with `--help` and a small test.
- The skill sends only to sessions and commands on the same machine. Nothing leaves your computer.
- To have your everyday Claude.ai chat give work to a running Claude Code session, see [CAAS](https://github.com/Rurimpa/caas). This skill is the idea CAAS is built on.

## License

MIT (`LICENSE`). When you use or modify this, keep the author name (Rurimpa) and this repository's address (https://github.com/Rurimpa/cross-session-messaging). Author: Rurimpa (https://github.com/Rurimpa)
