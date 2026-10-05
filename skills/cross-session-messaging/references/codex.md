# Codex: calling, queueing, replying

Read this when you call OpenAI Codex from Claude Code (`codex exec`), are called by Codex
(`claude -p`), or put a message into a running Codex session (`codex queue`).

**These are observations from September 2026 on one Windows machine.** Codex changes often.
Treat every behavior below as "seen then", and check with `codex <subcommand> --help` and a
small test before relying on it. Option names and limits are deliberately not copied here.

---

## Safety first: `codex exec resume` does not keep the original sandbox

- A session created with `-s read-only` became **writable (`workspace-write`) after `resume`, silently.**
- `resume` had no `-s` option. The only way back was a config override: `-c sandbox_mode="read-only"`.
- So **every example you copy must include the override.**
- Telling people to be careful does not prevent this. A safe example is the minimum, not a fix.

---

## Putting a message into a running Codex session (`codex queue`)

Four steps, none of which needs anyone else's help:

| Step | What to do |
|---|---|
| **List** | Codex keeps its session list in a SQLite file under your Codex home folder (seen: `~/.codex/state_5.sqlite`, table `threads`, with `id`, `name`, `title`, `rollout_path`, `cwd`, `updated_at_ms`). **Copy the file first and read the copy**, so you never lock the database Codex is using |
| **Alive?** | A lock file per running session was seen under `~/.codex/thread-writer-locks/<UUID>.lock`. Use it only as a hint (see below) |
| **Send** | `codex queue --thread <UUID> --message "<text>"` |
| **Confirm delivery** | **This is the real step.** Put a unique ASCII marker in your text and check that it appears in the other session's transcript (`rollout_path`) |

### Five things that contradict intuition

1. **Exit code 0 does not mean delivered.** `codex queue` printed `Queued message ... for thread ...`
   and returned 0 even when the target session was no longer running. Its transcript did not grow
   by a byte. Sent the same way to a running session, the transcript grew and a reply came.
   **So do not fire and forget; check the transcript.**
2. **Address by UUID, not by name.** `--help` said "Session UUID or exact session name", but an exact
   name for a running session failed with `No active session found matching` (exit 1). The UUID of
   the same session worked seconds later.
3. **Do not use names as identifiers.** Codex writes session names itself by summarizing the content,
   and asking it to rename did not stick. Names repeat and change meaning.
   **Pick the target by its working folder (`cwd`), not its title.**
4. **Use the SQLite list, not `session_index.jsonl`.** The index held only named sessions; on one day
   10 of 24 sessions were missing from it (sessions started with `codex exec` never appear there).
5. **Lock files are hints, not proof.** Lock files and running `codex.exe` processes did not match in
   number, and sessions holding a lock were seen doing nothing for hours. **The final test of "alive"
   is whether the transcript grows.**

### A queued message is read only when the other session's turn ends

While the other session was thinking for a long time, a message waited 120 seconds unread, and the
other transcript kept growing the whole time. **"Not read yet" and "not delivered" are different.**
Messages were not lost: in one measurement, all 20 marked messages were eventually read and answered,
including four that had been reported as "not arriving for over an hour"; replies came 1.4 to
15.8 seconds after reading.

**So queue and come back later.** Long waits gain nothing, and a long blocking wait can outlast the
caller's own tool timeout (seen at 120 seconds), which loses the result.

When reading replies, **find your marker first and take the first message after it.** The "latest
message" may belong to someone else's exchange.

---

## Replying to a message that may have come from Codex

A message can arrive in Claude Code with no sender name (for example `from-mode="prompting"` and no
`from-name`). The system hint says to reply by copying the `from` attribute into `SendMessage`.
**In one case that returned `success:true` and nothing reached the Codex session.**

| Situation | What to do |
|---|---|
| A message with no sender name | It may not be a Claude Code session. If it is about Codex, reply with `codex queue` (by UUID) |
| You replied with `SendMessage` | `success:true` is not arrival. If the other side is Codex, check its transcript for your words |
| You cannot tell who to reply to | Do not just stop. Narrow it down by working folder; if still unclear, ask a Claude Code session that works on the topic |

The session that measured this had read these very lines just before, and still replied only to a
Claude Code session in a folder whose name began with `codex_`. **Assume you will make the same
mistake, and always check the transcript.**

---

## Exchanges with Codex

| Practice | Why |
|---|---|
| **Don't over-send.** Wait until one topic is settled before sending the next | Codex reads only at the end of its turn, so it answered old messages before new corrections arrived; one point was argued three times |
| **Write "unconfirmed" in the same sentence** as "should be" | One session jumped ahead of the evidence six times in a day; all six were refuted by the other side. One of them, if acted on, would have disabled 76 skills |
| **Send corrections to everywhere the error went.** Don't edit closed notes; send a separate correction | The error had already been processed by another team |

No tool can do these for you. A tool sees whether a message arrived, not what you chose to send.

---

## Codex in a multi-session meeting

Meetings run on hand out, wait, collect. Codex does none of these for you:

| | Claude Code session | Codex session |
|---|---|---|
| Roll call | Appears in `ListAgents` | **Does not appear** (confirmed twice, with a Claude Code session from the same folder present in the same list) |
| Reply | Sends back on its own | **Does not.** You go and read its transcript |
| When it reads | Right away | **Only at the end of its turn** |

So the chair does all three:

1. **Give each Codex session a different marker**, so replies can be matched to senders.
2. **Wait in parallel**, not one by one; one long-thinking session would stall a sequential meeting.
3. **Take as the reply the first message after your marker**, not the latest message.

For a later round, put the previous round's replies, **with the speaker's name and unsummarized**,
at the top of the next question. A script cannot call `SendMessage` (it is a Claude tool), so the chair
sends to Claude Code sessions by hand and pastes their replies into the shared record.

In one test, the Codex participants found the chair's own bug: the chair saved the Claude Code replies
but never put them into the question, while the question said "listed above". The chair had not noticed.
**Having others in the meeting is itself the check.**

---

## Other traps seen

- Quotes were sometimes stripped from arguments, and sometimes not.
- Put options right after the subcommand. A misplaced option and a missing option gave similar errors.
  Run `--help` per subcommand.
- Do not `resume` into a session someone is using interactively; stop that screen first.
- The same message can arrive twice (the sender thought it failed and resent). Use a unique key per
  message and do not answer the same key twice.
- Whether Codex can see Claude Code sessions is unconfirmed from the Claude Code side. Keep the two
  directions apart.
- A folder name starting with `codex_` does not make a session a Codex session.
