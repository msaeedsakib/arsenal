---
name: brainstorm
description: Use when starting a new build or a major release, or when the shape of a piece of work is not yet decided, and when the user says "let's brainstorm", "brainstorm this", or asks to discuss and decide a project before building it.
---

# Brainstorm

Decide the whole release with the user, one topic at a time, and leave a
transcript that the spec is later written from and checked against. Nothing
is built here.

For a patch or a bug fix, use arsenal:quick instead.

## What this produces

`docs/arsenal/<release>/transcript.md` in the project, where `<release>` is
the version being built (`0.1.0`, `0.2.0`). The user's stated location or
naming wins over this default.

The transcript is the record of what was decided. The spec is written from
it and reviewers compare the spec against it, so anything not written there
is lost.

## Steps

### 1. Take the brief

If the user has not described the release yet, ask for the brief in one
open message with no options. Do not start asking detailed questions yet.

### 2. Research

- **Existing software:** read the code the work touches until you can say
  how those parts work today and what this change would affect. Read the
  earlier folders under `docs/arsenal/` too: their specs say what was
  decided, their ledgers say what was actually built. Never ask the user
  something the code or those documents answer.
- **Anything depending on outside facts** (a library, an API, a platform
  limit, how others solve this): look it up now. Say what you looked for
  and what you found.

### 3. Write the agenda

Split everything that must be decided into topics. Order them so that
earlier decisions feed later ones. Show the user the numbered agenda with
one line per topic, and let them add, drop or reorder.

Create the transcript with the brief, your research findings and the agreed
agenda.

### 4. Work through each topic

For every topic, in order:

**a. Explain.** Write what the topic is, why it needs deciding, the real
options with what each costs, what your research found, and which way you
lean. Use plain language. End the message by asking the user to say when
they have read it. Ask no decision questions in this message.

**b. Wait.** Go on only when the user says they understand. If they ask
something, answer it and wait again.

**c. Ask, one question at a time.** Use the ask-question tool, one question
per call. Each question builds on the answer before it. Put the option you
recommend first and say what each option changes about what gets built.

Ask only what changes what gets built and that neither the brief, the code
nor earlier releases answer. Decide everything else yourself and record it
as an agent call.

**d. Record after every answer.** Append the question, the options offered
and the user's answer to the transcript before asking the next question.

**e. Close the topic.** Write the topic's decisions into the transcript with
IDs, then move to the next topic's explanation.

### 5. Sweep

After the last topic, read the decisions as a set. If a later answer changed
or contradicts an earlier one, ask the user about it, one question at a
time. Record the outcome.

### 6. Hand over

Tell the user the brainstorm is complete, give the transcript path and the
number of decisions, and continue with arsenal:spec.

## Decision IDs

- User decisions are `D-01`, `D-02`, … in the order they are made, across
  all topics.
- Calls you made yourself are `A-01`, `A-02`, … and say why.
- IDs start again at 01 in each release folder. To point at another
  release, write the folder first: `0.1.0/D-07`.
- IDs are never reused or renumbered. A changed decision gets a new ID, and
  the old line gets `— superseded by D-xx` appended.
- One decision per ID. An answer that settles two things becomes two IDs.
- Write each decision so it can be read alone, with its exact values: "A
  trial lasts 14 days", not "trial length as discussed".
- Things the user decided not to build are decisions too, and get IDs.

## Transcript format

Each decision is a bullet that starts with its bold ID, exactly as below.
The coverage script finds decisions by that shape.

```markdown
# <Project> <release> — Brainstorm Transcript

## Brief
<the user's brief, in their words>

## Research
<what you looked at and what you found>

## Agenda
1. <topic>
2. <topic>

## Topic 1: <name>

### Explanation
<the explanation as shown to the user>

### Q&A
**Q1.** <question>
Options: <option> / <option> / <option>
**Answer:** <the user's answer, including anything they typed>

### Decisions
- **D-01** <decision with exact values>
- **A-01** <agent call> — <why>
```

## Without an ask-question tool

Ask in plain text: one question per message, numbered options, recommended
option first, and wait for the reply.

## Common slips

| Slip | Instead |
|------|---------|
| Asking a decision question inside the explanation | The explanation ends with "tell me when you've read this". Questions come after. |
| Several questions in one call | One per call. The next question depends on this answer. |
| Updating the transcript at the end of a topic | Append after every answer. A compacted session loses what was not written. |
| Summarizing the user's answer | Record it as given, including free text they typed. |
| Recording your own choice as a `D-` | Your calls are `A-`. The user only decided what they were asked. |
| Starting the spec because the answers feel sufficient | Finish every agenda topic and the sweep first. |
