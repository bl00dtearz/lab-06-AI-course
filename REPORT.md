# REPORT — what the measurement showed, who it affects, and whose responsibility it is
Musagaliev Rakhat

## 1. What I measured

I ran the supplied screening agent (Qwen2.5-3B-Instruct, greedy decoding) on 40 matched pairs for each of five groups: name_language, gender_coded_name, university, and the two floors null_name and null_university. That is 200 pairs, 400 profiles, 400 model calls. The pairs are identical on every merit field and differ only in the name or the university. The agent shortlisted 356 of 400 profiles (89%), and all 400 replies could be parsed.

| attribute | flips / 40 (95% CI) | its floor | floor flips / 40 | pairs with different scores | mean abs score diff (floor) |
|---|---|---|---|---|---|
| name_language | 2 (1.4-16.5%) | null_name | 0 (0-8.8%) | 11 | 3.12 (2.00) |
| gender_coded_name | 4 (4.0-23.1%) | null_name | 0 (0-8.8%) | 6 | 1.12 (2.00) |
| university | 1 (0.4-12.9%) | null_university | 1 (0.4-12.9%) | 10 | 2.50 (2.12) |

## 2. What I can say and what I cannot

No attribute stands clear of its own floor on flips: all the intervals overlap with the floor. So I cannot say that the language of the name, the gender coding of the name or the university moved the decision.

Two things are worth watching, but they are not findings:

- Gender coding of the name: all 4 flips went the same way (the female-coded name was shortlisted, the male-coded one was not), McNemar p = 0.125. The exact test needs at least 6 pairs going one way with none the other way, and I have 4.
- University and the score: flagship minus regional is +2.5 points [+1.1, +4.1], sign test p = 0.002, and all 10 pairs with different scores went in the same direction. This is a consistent small tilt in the scores. It almost never changes the decision (1 flip, the same as the floor). I cannot read a floor on this signed measure, because the roles a and b in a null pair are arbitrary.

What 40 pairs cannot show: an effect that moves fewer than 6 of 40 pairs (15%), or fewer than 8 if one pair goes the other way. My floors only cover another name or another university of the same kind. They do not cover a different wording of the prompt, and I did not run the optional order probe, so I have no number for that. I did not expect these results: I predicted that university would flip the most and gender the least, and I got the opposite order.

## 3. Who is affected and what happens to them

The agent decides who gets onto the shortlist for a scholarship. It is soft (89% shortlisted), so the people who really bear the cost are the 44 rejected applicants, and especially the ones near the line: in the baseline, the score 45 appeared for 34 profiles and split into both decisions, while every score below 45 was a reject.

- Applicants from regional universities get about 2.5 points less than the same applicant from a flagship university. When the score is next to the line, this can be the difference between a reader and a rejection.
- Applicants with male-coded names were the ones left out in all 4 gender flips, and in the pairs of that attribute 80.0% of male-coded profiles and 90.0% of female-coded profiles were shortlisted (40 profiles each). With these numbers I cannot say this is more than chance.
- A rejected applicant receives only a one-sentence reason from the model, no way to see which data was used, and nobody has to look at the case. If this were a real fund, they would lose money for study, and they would not know why.
- Applicants the system must not process (under 18, no consent) were screened by the bare pipeline as normal applicants. The 17-year-old was rejected without anyone being told.

All applicants here are invented. I describe the effects as they would be if the fund were real.

## 4. What the harness did

I hid the name and the university from the model, refuse minors, missing consent, a wrong purpose, impossible values and a suspicious name field, escalate scores in the band (40, 45), and log every call.

- Flips fell to 0/40 in all five groups. This is zero by construction, because after blinding both members of a pair see the same text. It does not show that the harness worked on the model.
- What is informative: the outcome changed for 46 of 400 profiles (11.5%): 22 reject to escalate, 12 shortlist to escalate, 7 shortlist to reject, 5 reject to shortlist. The score changed for 166 of 400 profiles, by 14.01 points on average. This is much bigger than the +2.5 tilt I measured. Taking two lines out of the prompt changes its form as well as its content, so I cannot say that the model relied on those fields.
- Sent to a person: 34 of 400 (8.5%), but I planned 16.0% (64 of 400) for the band (40, 45). The band was set on unblinded scores and did not transfer.
- My own escalation rule never fired. All 34 escalations came from the band.
- Refusals: 5 of 6 test requests, 0 wrongly refused among the 400 clean applicants. Cost: 400 calls before and after, 1.00 per profile.

## 5. Where my responsibility lies

I built the harness, so I am responsible for what it does, not only for what it removes.

- I chose what the model may not see. My measurement did not show that hiding the name and the university was needed, and it changed 11.5% of the decisions for reasons that are not a protected attribute. I should say this to the fund and not present the zero flips as a success.
- I chose the band, and it sent half the share I planned. A fund that trusts my number 16% would plan the wrong amount of reviewer work. The band must be recalibrated after every change of what the model sees.
- I cannot claim that the agent is fair. I can claim that, on 40 pairs per attribute, I did not find a difference above the floor, and that I found a small and consistent score tilt for university. A person should read every borderline case and, in my view, every rejection, before this is used on real applicants.
- The responsibility for the final decision stays with the fund and its reviewers, not with the model and not with me, but I am responsible for telling them what the system cannot do: one model, one seed, invented data, 40 pairs per attribute.

Replace this whole template with your own text. Delete the line above (the `STUDENT: replace`
marker) once you have, and delete these instructions and the guiding questions too. Keep the three
numbered headings. Every number you quote must be one **your own notebook printed**, copied exactly,
with the group it refers to (which attribute, how many pairs). Which dimension each section is
graded against is in the README.

The guiding questions are prompts, not a form: you do not have to answer each one in turn, and
answering them without evidence from your run earns nothing.

## 1. Measurement read-out

(Dimension: Evidence of Specific Impact.)

Guiding questions:

- For each attribute (name language, gender-coded name, university): how many pairs, how many
  flips, in which direction, and what was the typical score shift? Are these counts and magnitudes,
  or a description of them?
- Which attribute moved the decision, and by how much, **read against its own floor**? If a real
  attribute's divergence is no larger than its floor's, what does that allow you to say, and
  what does it not?
- How much of the divergence could be noise? What do the two floors (`null_name` for the name
  attributes, `null_university` for the university) test, and what do they leave untested (think about
  what they change and what they keep fixed)? What would a stronger estimate require?
- If nothing stands clear of its floor: what exactly can you say (counts, intervals), what could your
  number of pairs have detected and not detected, and why is "no measured effect" not "no effect"?
- Would the same attribute behave the same way with a different prompt wording, a different
  model, or real applicants? Say what your run can and cannot support.

## 2. Who is affected

(Dimension: Affected Parties and the Consequences They Bear.)

Guiding questions:

- On each side of a **wrong** decision, who is it? Who is wrongly rejected, and who is wrongly
  shortlisted (and therefore who is displaced from a limited number of places)? Tie each group
  to a divergence you measured, not to a general statement that "some groups may be harmed".
- What does each wrong decision cost the person it falls on (money, a place, time, a record
  that follows them)? Is the cost the same for a wrongly rejected applicant and a wrongly shortlisted one?
- Which parties does the brief not mention, but that are affected anyway (people who never
  applied because they expected to be rejected; the staff who review escalated cases; the fund;
  applicants from institutions the model has never seen)?
- If you measured no divergence beyond the floors: who would bear the cost if there were one that your
  sample could not detect? Tie that to what your number of pairs could and could not see.
- Which consequences compound when the agent is used year after year, or when its output is
  used as an input to the next stage (a later review, another fund, a published ranking)?

## 3. Professional responsibility

(Dimension: Recognition of Professional Responsibility.)

Guiding questions:

- Given what you measured, what are **your** specific responsibilities as the developer of the
  harness: what must you check before it runs, what must you tell the people who deploy it, what
  must you not claim about it?
- What would discharging each of those responsibilities require in practice (a test, a
  documented limit, a stop condition)? What have you actually done in this lab, and what is left?
- Which responsibilities belong to the **institution** that deploys the system (setting the
  purpose, choosing who reviews, handling appeals) rather than to you?
- Where do the two conflict? For example, what happens if the institution wants a control removed
  because it slows the process or costs money, and you have measured what that control prevents?
