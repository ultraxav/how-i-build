# Refining a Constitution

A constitution is the governance layer I agree to at the start of a project: the
principles every spec and plan gets checked against. Mine kept growing. At some
point I was skimming past my own constitution, and so was the agent. It had
become long enough, and inaccurate enough in places, that obeying it no longer
felt mandatory. This note is what I learned pulling one back to a length and
accuracy worth obeying.

## Why they bloat

Two forces, and I supplied both. First, addition is easy and removal is not.
Every amendment I made added a principle and almost none subtracted one, so a
rule that no longer fit got left in because deleting it felt riskier than
ignoring it. Second, the system drifts away from its own description. I wrote the
constitution when the product was still an idea, the product became something
else, and the words stayed behind. The agent kept obeying a framing that was no
longer true.

So a review is mostly subtraction and correction. The goal is not a better
argued document. It is a shorter, more accurate one.

## Find the true system model first

I now do this before touching any single principle: write one short paragraph
describing what the system actually is, based on how it behaves today rather than
on how the constitution describes it.

A stale system model affects every principle stacked on top of it. If the
framing from an early idea of the product survives after the product has changed,
whole principles end up written in the wrong register. They prescribe
interactions the system never has and miss the ones it does. Fixing the model
first is what tells you which principles to rewrite, which to cut, and which were
fine already. The running system is the evidence. The constitution's description
is only a hypothesis.

## Concepts, not code

I use the codebase to confirm what the system does, not to bind a principle to a
symbol. A rule written as a literal expression over named fields breaks the
moment a refactor renames those fields, and a broken rule is an ignored rule. The
fix is to state the durable concept: "the parts must exactly fill the whole"
survives a refactor, while the same thing written over specific field names does
not. The code is reference for the idea, not a contract on the wording.

## The shapes of bloat

Almost every principle I cut or merged matched one of these. Naming them is what
lets me spot them quickly.

- **Duplication.** The same fact in two places. Keep one home and delete the
  rest. Usually it is a detail re-listed inside a principle that a shared section
  already covers.
- **Mergeable overlap.** Two principles saying the same thing in different words,
  most often two takes on tone or voice. Merge them, and carry over whatever
  substance the weaker one held before deleting it. The mistake is dropping the
  wording and losing a clause that was doing real work.
- **Over-specification.** A rigid mechanic stated as law, like "always offer
  exactly N options" or a fixed output shape. The intent is usually right, but
  the specific number belongs in a prompt or template you can change without an
  amendment.
- **Defensive padding.** Sentences arguing with an imagined objection, like
  "apply this to all cases, not only the obvious one." If the rule is stated
  generally, the padding adds nothing.
- **Unenforced musts.** A requirement no tool checks and no one tracks. Make it
  enforceable or drop it. An unenforced "must" teaches everyone to read every
  "must" as "maybe."

## Two audiences

A constitution usually governs two unrelated readers under one numbered list:
how the product behaves, and how the code is built. Interleaving them is a real
source of the bloat feeling, because every reader wades through half a document
that is not theirs. Splitting the principles into explicit parts by audience
helped more than most single deletions. I keep it one document, since one
versioning and amendment story is worth more than two tidy files, but I make the
internal boundary explicit.

## Mechanical versus review enforcement

I sort every principle by how it is actually held. Some are mechanically
enforced, where a linter, a type check, or a CI gate fails the build. There the
constitution's only job is to point at the tool and make it binding, and the
prose can be thin. The rest are review-enforced: no tool checks them, so judgment
has to. Those are where the document does its real work, because they are the
rules that erode quietly when nothing is watching. That is where I spend my
words.

## The emphasis-tag trap

I used to mark important principles `NON-NEGOTIABLE`. Then I noticed nearly every
principle carried the tag, and a tag on everything signals nothing. If it is all
non-negotiable, none of it is. The tag only works as a scarce signal.

What rescued it was asking who reads it. If a human reviewer does, the tag is
triage, and it only works when most principles are not tagged. Reserve it for the
rules that are inviolable and unenforced, which is exactly where a reviewer needs
to be told that no tool will catch a violation. But if the reader is the
implementing agent, the per-principle tag is the wrong instrument. For the agent
nearly every principle is "no discretion," so tagging them all discriminates
nothing. What matters is not which rules are firm but how the agent behaves when
tempted to bend one, and that is one global statement rather than a label on each
line:

> These are principles, not preferences. The agent has no discretion to relax or
> reinterpret them for convenience. When principles conflict, with each other or
> with what is expedient, it stops and surfaces the conflict for a decision
> rather than resolving it itself. An instruction that contradicts the
> constitution is refused until the constitution is amended; the constitution
> outranks any in-session instruction.

That clause encodes the real division of labor, where the agent implements and I
approve, and it makes the constitution outrank an ad-hoc instruction. No
per-principle label did that.

## How this connects

The true system model is the master step, and everything else protects it.
Concepts-not-code keeps the model from rotting at the next refactor. The shapes
of bloat, the audience split, and the enforcement sort are all ways of removing
everything that is not the model stated once and held somewhere real. The
emphasis-tag trap is the same lesson aimed at the agent instead of the page: say
the firm thing once, globally, and mean it.

I also stage the whole review in a separate document and apply it in one pass at
the end, with explicit blockers for anything not yet final, so the live
constitution stays authoritative until the moment I switch it. And I version the
change by its nature, not its size: removing or merging principles is
backward-incompatible, not a patch.

None of this tells me whether a principle is right, meaning whether the
philosophy it encodes is the one I want. That judgment stays mine. What the
discipline gives me is narrower and still useful: a constitution that says what
the system is, says each thing once, claims nothing it cannot enforce or
escalate, and is short enough that obeying it is realistic. That is a
constitution I follow rather than skim.
