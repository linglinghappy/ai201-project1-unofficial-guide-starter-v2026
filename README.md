# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This tool answers questions about student life at one university. It uses 88 short student posts about courses, dorms, dining halls, and campus rules. It finds the best posts and writes an answer with the source file. If the posts do not cover the question, it says "I don't have enough information about that."

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

## Chunking Strategy

**Chunk size:** 178 to 549 charaters
**Overlap:** 0 

Every post is short, under 550 characters, so one post is one chunk. This size is easy to read and easy to test. Some chunks cover more than one topic, and that is fine for now. I keep the title inside each chunk. I also keep the "Re:" follow-ups as separate chunks, because they repeat the key facts.

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `admin_add_drop_deadline.txt` — produced by: `chunker.py::split_documents`

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160.txt` — produced by: `chunker.py::split_documents`

```
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_hist_118_workload.txt` — produced by: `chunker.py::split_documents`

```
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt` — produced by: `chunker.py::split_documents`

```
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** — source: `housing_innisfree_hall.txt` — produced by: `chunker.py::split_documents`

```
Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

The bad: no air conditioning, which matters for the first three weeks of September.

Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** What is the workload of course CS 340?

**Answer:**

```
The workload for CS 340 is 6 hours a week early on, and 15 hours a week in the last three weeks when the project lands. It is front-loaded, making the first month heavier than the rest. (Source: course_cs_340_workload.txt)
```

**My relevance cutoff:** 0.45

I ran 5 test questions and the 5 `OUT_OF_SCOPE` questions.

- Questions the corpus covers: best distance 0.234 to 0.377.
- Questions it does not cover: best distance 0.825 to 0.934.
- The two groups do not overlap. The gap is 0.377 to 0.825.

I set the cutoff at 0.45. It is above my highest good distance. The cost is a small margin (0.073). A badly worded question could be refused by mistake.

The cutoff cannot catch near misses. I asked about things that do not exist:

- "CS 999" had distance 0.331.
- "Happy Hall" had distance 0.337.

Both are lower than my real job-hours question (0.377), so the gate let them through. The grounding instruction stopped both. The model said it did not have enough information. I tested only two cases, so I did not change `GROUNDING_INSTRUCTION`.

I set `TOP_K` to 3. The right chunk was in the top 3 for all 5 test questions. The cost: CS 340's main page (rank 4 for Q1) is no longer retrieved.

<!-- The number you set in config.py, and how you got there.

     You ran five questions your corpus covers and the five in OUT_OF_SCOPE
     that it clearly doesn't, and wrote down the best distance for each. What
     did those two groups look like? Where was the gap? Put the actual numbers
     here — the table below wants all ten rows.

     Milestone 4. -->

| Question | In corpus? | Best distance |
|---|---|---|
| What is the workload of course CS 340? | Yes | 0.288 |
| How long is the wait at the Halden Hall | Yes | 0.234 |
| How much does one dryer cycle cost in the Morrow House laundry? | Yes | 0.265 |
| What is the maximum number of hours I can work an on-campus job per week | Yes | 0.377 |
| How long does it take to walk from Fenwick Court to central campu? | Yes | 0.313 |
| What is the capital of Mongolia? | No | 0.825 |
| How do I change the oil in a diesel engine? | No | 0.934 |
| Who won the 1994 World Cup? | No | 0.886 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.844 |
| How do I write a for loop in Rust? | No | 0.896 |

## How I Used AI

<!-- Two specific moments. For each: what you asked for, what came back, and
     what you changed about it.

     "I asked Claude to write the chunking function from my notes. It ignored
     the overlap, so I added that myself" is the level of detail we're after.
     "I used AI to help me code" is not.

     Milestone 5. -->

**1.** I asked Claude to revise my criteria #4 and #5. It suggested "no chunk is shorter than 250 characters" for #4. Before I used it, we checked my real chunk lengths. 17 of my 88 posts are under 250 characters, and they are still complete answers (one is 178 characters). A 250 minimum would have failed good chunks. I changed criterion #4 to: in 10 sampled chunks, at least 8 read as a complete thought, no chunk is shorter than 100 characters, and every chunk keeps its title line.

**2.** When I set the cutoff, Claude first said a lower cutoff would also catch questions about campus topics my posts do not cover. I tested that with a course and a dorm that do not exist ("CS 999" and "Happy Hall"). Their distances were 0.331 and 0.337, both lower than my real job-hours question (0.377), so no cutoff could separate them. The grounding instruction stopped both instead. I dropped that claim, wrote the result in the README, and kept the grounding instruction as it was.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 2. Every answer names a source | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |
| 4. Sampled chunks are complete thoughts, ≥100 chars, title kept | 8 of 10 | 10 of 10 | 10 of 10 | 10 of 10 | MET |
| 5. Every number in the answer appears in the retrieved chunks | 5 of 5 | 5 of 5 | 5 of 5 | 5 of 5 | MET |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->
The three runs were real re-runs: `run_eval.py::run_once` calls `answer_from_chunks(..., cache=False)`, the terminal reported 15 model calls (5 questions × 3 runs), and the answer wording differs between runs (see CS 340 below). Criteria 1, 3 and 4 test retrieval and chunking, which are deterministic, so the same number goes in all three columns.

**Criterion 1: Retrieved chunk contains the answer.** From `store.py::search`, top-k 3 (results/run_2026-09-23_1940_before.md):

```
What is the workload of course CS 340?
- Sources retrieved: course_cs_210_workload.txt, course_cs_340_exams.txt, course_cs_340_workload.txt
How long is the lunch wait at the Halden Hall
- Sources retrieved: dining_halden_hall.txt, dining_halden_hall_followup.txt, dining_pellew_dining_hall_followup.txt
How much does one dryer cycle cost in the Morrow House laundry?
- Sources retrieved: housing_aldridge_hall_laundry.txt, housing_morrow_house_laundry.txt, housing_old_brewhouse_laundry.txt
What is the maximum number of hours I can work an on-campus job per week
- Sources retrieved: course_phys_130_workload.txt, course_stat_150_workload.txt, money_jobs.txt
How long does it take to walk from Fenwick Court to central campu?
- Sources retrieved: housing_fenwick_court.txt, transit_shuttle.txt, transit_walking.txt
```

The file holding the answer is retrieved for all 5 questions, in all 3 runs.

**Criterion 2: Every answer names a source.** From `generate.py::answer_from_chunks`, run 1:

```
The workload for CS 340 is 6 hours a week early on, and 15 hours a week in the last three weeks when the project lands. It is front-loaded, making the first month heavier than the rest.
Source: `course_cs_340_workload.txt`

The wait time at Halden Hall is rarely more than 8 minutes, even at noon.
Sources: `dining_halden_hall.txt` and `dining_halden_hall_followup.txt`

One dryer cycle in the Morrow House laundry costs $1.25 (housing_morrow_house_laundry.txt).

The maximum number of hours you can work an on-campus job is 20 hours a week during the term (Source: money_jobs.txt).

It takes 18 minutes to walk from Fenwick Court to central campus.
Source: `transit_walking.txt` (also mentioned in `housing_fenwick_court.txt`).
```

**Criterion 3: Gate stops out-of-corpus questions.** From `run_eval.py::check_out_of_scope`, cutoff 0.45:

```
refused  (best distance 0.825)  What is the capital of Mongolia?
refused  (best distance 0.934)  How do I change the oil in a diesel engine?
refused  (best distance 0.886)  Who won the 1994 World Cup?
refused  (best distance 0.844)  What is the recommended dosage of ibuprofen for a headache?
refused  (best distance 0.896)  How do I write a for loop in Rust?
-> gate refused 5 of 5
```

**Criterion 4: Sampled chunks.** From `chunker.py::split_documents`: 88 posts → 88 chunks, shortest 178 characters. Two of the 10 I sampled:

```
On the meal plan changes

You can change your meal plan tier once, in the first ten days of the semester. After that it's locked. Downgrading refunds the difference to your student account; upgrading bills you immediately.
```

```
Laundry in Morrow House

Machines take $1.50 wash, $1.25 dry, coin or card. There are eight washers and six dryers for the building, which is the wrong ratio and means the dryers back up on Sunday evenings.

Best time to do laundry here is Tuesday or Wednesday morning. Sunday after 6pm you will wait.
```

**Criterion 5: Numbers in the answer appear in the chunks.** Answer from `generate.py::answer_from_chunks` next to the chunk text from `store.py::search`:

```
CS 340    answer: "6 hours a week early on ... 15 hours a week in the last three weeks"
          chunk:  "6 hours a week early, 15 in the last three weeks when the project lands."
Halden    answer: "rarely more than 8 minutes"
          chunk:  "Wait times: rarely more than 8 minutes, even at noon."
Morrow    answer: "costs $1.25"
          chunk:  "Machines take $1.50 wash, $1.25 dry, coin or card."
Job hours answer: "20 hours a week during the term"
          chunk:  "Maximum is 20 hours a week during term."
Fenwick   answer: "It takes 18 minutes"
          chunk:  "Fenwick Court to central campus: 18 minutes."
```

**Note on `scorer.py::judge`.** It checks whether the answer contains the `expects` phrase, and it scored 4 of 5 in every run. CS 340 failed all three times even though all three answers were correct. My `expects` phrase was "6 hours weekly early, 15 hours in the last three weeks", and the model never writes it word for word. That is a false fail from the substring test, not a wrong answer.



## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
