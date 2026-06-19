# UX Primer

Six core concepts, selected because each one explains a way my work currently
goes wrong. This is not the whole field. It is the foundation I need first,
chosen by relevance to real failures rather than completeness. Written to be
used by me (to learn) and by an agent (to apply).

Each concept has the same three parts: what it is, the failure it explains, and
what it tells me to do.

---

## 1. Cognitive load

**The concept.** Every element on a screen costs the user a small amount of
attention to process. That budget is finite. The interface spends it whether or
not the design intended to.

**The failure.** "Too many ideas." Each idea was clear to me, so each got a place
on the screen. The cumulative result overspends the user's attention. A screen
that felt complete to its author is exhausting to a newcomer.

**What it tells me to do.** Treat density as a cost, not an achievement. Before
adding anything, ask what it costs the user to process. The default move is
removal.

---

## 2. Mental models and the curse of knowledge

**The concept.** A user acts on their internal picture of how the thing works.
The author has a complete, accurate picture and cannot un-know it, so the author
is the worst judge of whether the interface is clear.

**The failure.** The app made sense to me and bewildered others. I only found the
flaws when I explained it to someone whose head didn't already contain my model.
The clarity was in me, not in the screen.

**What it tells me to do.** Assume my sense of "obvious" is unreliable. The real
test is a person with no context using it without my narration. Design for the
model they arrive with, not the one I built.

---

## 3. Affordances and signifiers

**The concept.** An affordance is what an element lets you do; a signifier is the
visible cue that tells you it's there. Users don't read interfaces, they scan for
what looks actionable. If the cue is missing, the action effectively doesn't
exist.

**The failure.** "Unintuitive." Where users didn't know what to do, it was rarely
that the action was absent, it was that nothing signaled it. The interface knew
what was clickable; the user didn't.

**What it tells me to do.** Make actionable things look actionable, and make
non-actionable things not. When something is "unintuitive," look for the missing
signifier before assuming the user is lost.

---

## 4. Consistency and conventions

**The concept.** Doing the same thing the same way every time, and following
patterns users already know from elsewhere, removes the cost of re-learning.
Every inconsistency forces the user to stop and re-interpret.

**The failure.** "Inconsistent." The same kind of task was handled differently in
different places, so each screen had to be figured out fresh instead of
recognized. Visual consistency existed (the design system); behavioural
consistency did not.

**What it tells me to do.** Pick one way to do a recurring thing and repeat it.
Prefer conventions users already know over novel solutions. Consistency is the
behavioural counterpart to the design system.

---

## 5. Feedback and system status

**The concept.** After any action, the user needs to know what happened, and
during any wait, they need to know the system is working. An interface that goes
silent leaves the user guessing whether it heard them.

**The failure.** Confusing flows around things that take time (generation,
analysis, async work). The system was doing the right thing internally, but
whether the user could *feel* that it was working was an afterthought.

**What it tells me to do.** Every action gets a visible response. Every wait gets
a visible sign of progress. Never let the interface go quiet while it works.

---

## 6. Hierarchy and progressive disclosure

**The concept.** Not everything deserves equal weight or equal presence. The most
important thing should be the most prominent, and complexity should be revealed
as the user needs it, not dumped all at once.

**The failure.** The screens that grew into tangles (an editor that became an
800-line everything-at-once surface). With no hierarchy and nothing deferred,
every feature competed for the same attention at the same time.

**What it tells me to do.** Decide what the one most important thing on a screen
is, and make it read first. Hide or defer the rest until it's needed. This is the
direct antidote to "too many ideas."

---

## How the six connect

Cognitive load is the master concept; the other five are specific ways to
protect it. Mental models explain who you're protecting it for. Affordances,
consistency, feedback, and hierarchy are four levers that each lower the load:
by signaling clearly, by not forcing re-learning, by keeping the user informed,
and by not showing everything at once.

---

## Roadmap

The six above are the foundation. The rest of the field is deferred on purpose.
Each item below has a trigger: the point at which it stops being abstract and
becomes worth learning.

- **Accessibility** — when there are real users. Contrast, tap targets, keyboard
  and screen-reader support become concrete the moment a real person can't use
  the interface.
- **Typography and color as communication** — after the six are internalized.
  How type and color carry meaning and hierarchy, beyond the design system's
  token choices.
- **Information architecture** — when an app grows past a handful of screens.
  How content and navigation are structured so users can find and locate
  themselves.
- **Interaction and input patterns** — when building forms and complex inputs.
  Established patterns for entry, selection, and error handling.
- **User research methods** — when ready to test deliberately rather than
  accidentally. How to watch real users, run sessions, and turn observation into
  changes. This is the eventual ground truth that validates everything else.
- **Error prevention and recovery** — when flows get destructive or
  irreversible. Designing so mistakes are hard to make and easy to undo.

Climb in roughly this order, triggered by need rather than by schedule. When a
new wall appears that none of the six explains, the concept that explains it is
the next thing to learn.
