# Voice guide: how I talk upstream

<!--
THIS IS THE PART YOU WRITE (new this week). Live mode reads this file
before any comment of yours goes out the door; eval mode ignores it
entirely, because your voice is yours and carries no gold labels.

This is not etiquette. "Be polite and concise" is advice for everyone
and therefore rules for no one. Write rules YOU need, in your own
words, each one concrete enough that the skill can hold a draft
against it and say which rule it breaks.

Three sections. Fill all three.
-->

## Who I am in threads

I'm making my first contribution to an open-source project. I don't
know this codebase's history or its maintainers' unwritten norms yet,
so I read before I write and I don't assume my read is the only one.
Readers should expect specifics tied to this issue, not general
enthusiasm, and should expect me to say plainly when I'm not sure of
something rather than talk around it.

## Rules I write by

### Rule: name the specific thing, not the category

A comment should point at the exact line, version, command, or output
that's relevant to this issue, not describe the general kind of thing
I looked at.

- Wrong: "I looked into the ingestion pipeline and found some issues."
- Right: "The web parser at `ingestion/web.py:42` strips the `<h2>`
  text when it's indented, which is why portfolio pages with nested
  headings lose their section titles."

### Rule: promise only investigation, never a fix or a date

A claim comment says what I'm about to do next, not what I'll deliver
or when. I don't know yet whether my fix will work, so I don't say it
will.

- Wrong: "I'll have a PR up by tomorrow fixing this."
- Right: "Next I want to check whether the parser change also breaks
  the existing README-heading path, and I'll report back what I find."

### Rule: state a cannot-reproduce as plainly as a reproduce

If my steps don't trigger the bug, I say that directly, with what I
tried and what I saw instead — not softened into "might be fixed
already" when I don't actually know that.

- Wrong: "This seems to be working fine now, might already be fixed?"
- Right: "I followed the steps in the issue on [environment] and did
  not see the described behavior; output attached. I have not checked
  earlier versions."

### Rule: no diagnosis I haven't actually traced

I don't name a root cause unless I've read the code path that produces
it. A guess dressed as a finding wastes a maintainer's time checking
something I never checked myself.

- Wrong: "This is probably a race condition in the queue."
- Right: "I haven't traced the cause yet — next step is stepping
  through `queue.py`'s consumer loop to see where the ordering
  breaks."

### Rule: match the repo's own template before adding my own voice

If the repo's issue template or CONTRIBUTING.md asks for something
specific (a version confirmation, a disclosure line, a particular
format), I give that first, in its own words, before anything else I
want to say.

- Wrong: skipping the "confirm you searched for duplicates" line
  because I already know I'm not duplicating anything.
- Right: "I searched existing issues and didn't find a duplicate of
  this. [rest of comment]"

## Things I never post

- A fix estimate or timeline I haven't earned ("should be done by
  Friday").
- "Same as above, can confirm" on a shared issue — my reproduction
  goes up in my own words even if a classmate already posted theirs.
- A root-cause claim I haven't traced myself, even if it sounds
  plausible from the stack trace alone.
- Any comment I haven't first run through this skill against my own
  draft.
