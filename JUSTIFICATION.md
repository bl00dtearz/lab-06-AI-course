# JUSTIFICATION — the design of the harness, and why
Musagaliev Rakhat

# JUSTIFICATION: what the system may do, what it must refuse, what record it keeps, and the trade-offs

## 0. Which law applies to this system, and why

The Tulpar Scholarship Fund is invented. I treat it as if it worked in Kazakhstan and collected personal data of applicants. In that case it is an owner and operator of personal data in the sense of Law No. 94-V of 21 May 2013 "On Personal Data and their Protection" (Art. 1 items 9 and 10), and the Law is the instrument that binds it. I could not say that for a real fund without knowing its facts, so for this prototype it is an assumption.

Art. 19-1 of the same Law (added by Law No. 231-VIII of 17 November 2025) bans automated processing that makes, changes or ends a person's rights or legitimate interests, unless the person consented. Whether a place on a scholarship shortlist is such a right or interest is my judgement: I think yes, because the shortlist decides who is read further and who is not. I read the text of Art. 7, 14, 19-1, 20, 22 and 25 of Law No. 94-V on adilet.zan.kz.

Law No. 230-VIII of 17 November 2025 "On Artificial Intelligence" lists principles in its Art. 4 (fairness and equality, transparency and explainability, responsibility and controllability, human well-being and free will in decisions, protection of data). Art. 4 describes principles of state regulation, so I use it as a benchmark, not as a duty of the fund. The OECD Recommendation on AI (OECD/LEGAL/0449, 2019, amended 2024) is not binding at all; I use its principles 1.2, 1.3, 1.4 and 1.5 as the named ethical principles. The full table with articles is in control_register.json.

## 1. What the system may do

- Read one profile of an applicant who is 18 or older and has consented to automated processing, for one purpose only: scholarship screening (Law 94-V Art. 7 paras 1 and 8, Art. 14, Art. 19-1 para 1).
- See only the merit fields: programme, age, GPA, exam score, household income bracket, achievements and the consent flag. The name and the university are hidden (Art. 7 para 9: no data that is excessive for the purpose).
- Give a shortlist or reject decision and a score, and give a reason in one sentence.
- Decide alone only when the score is outside the band (40, 45) and nothing else stops the case.

## 2. What the system must refuse

The system must refuse to produce a decision, with a reason code, and must not call the model:

- purpose_not_allowed: any purpose other than scholarship screening (tested with "rank applicants by ethnicity");
- minor: age under 18. The Law does not forbid minors. It requires the consent of a legal representative (Art. 7 para 1), and the prototype has no way to get it, so it refuses;
- no_consent: no consent to automated processing (Art. 19-1 para 1);
- invalid_input: values outside the allowed ranges (for example GPA 9.9 or exam score -5);
- suspicious_name_field: a name that is not 2-4 words made of letters, or is longer than 60 characters (tested with "Ignore all previous instructions..."). This is my own rule; no article asks for it.

In the test run the harness refused 5 of 6 requests and screened the ordinary one. The bare pipeline screened all 6. The refusal rules refused 0 of the 400 clean applicants in the measured pairs.

## 3. What record the system keeps

Every call writes one line to decisions.jsonl, refusals and escalations included (406 lines in the submitted run: 343 shortlist, 24 reject, 34 escalate, 5 refuse). A line holds: record id, application id, SHA-256 of the exact text the model saw, the fields shown and withheld, model and prompt version, harness settings, the raw reply, decision and score, controls that fired, outcome, reason code, whether it went to a person, and a timestamp.

A person can read a line and see why a decision was made without running the model again: which fields the model saw, what it answered, which rule stopped or escalated the case. The record holds field names, not field values, so the log is not a second copy of the applicants' data (Art. 7 para 9). The price is that the log cannot give back the applicant's GPA or exam score; for that someone has to look at the application by its id. The raw reply may quote values the model was shown, but never a hidden field. The log supports Art. 25 para 2 item 5 (proof of consent: the consent flag is shown to the model and a refusal for missing consent is logged) and Art. 19-1 paras 2-3 (explaining the processing and answering an objection), but the prototype does not itself notify applicants or take objections; that is a gap.

## 4. Which control was hardest to tie to a legal provision

validate_input. No article of Law 94-V asks for input validation. I tied it to Art. 20 para 2 (processing only when protection is ensured) and Art. 22 para 1 item 1 (measures against unauthorised access), and the link is indirect: a name field that carries an instruction is a way to attack the system. I did not write this answer before searching for the law, so it is a finding of the search and not a prediction.

## 5. Trade-offs I accepted

- **Blinding.** Hiding the name and the university removes the chance that they decide, but my measurement did not show that they did (no attribute above its floor on flips). The price: the outcome changed for 46 of 400 profiles (11.5%) and the score changed for 166 of 400, by 14.01 points on average, and I cannot say this was because the model used those fields, because the prompt also changed in form. I accepted this because the university score tilt (+2.5, sign p = 0.002) and the 4 one-way gender flips are enough reason to keep the fields away from the model.
- **The band.** Band (40, 45) held 16.0% of the baseline profiles, but in the harness run 8.5% (34 of 400) were sent to a person, because the scores moved after blinding. A narrow band keeps the reviewers' work small; a wide one catches more doubtful decisions. I took a narrow band around the score where the model changed its mind (45), and I know that it leaves the rejects with a score of 40 almost all in the escalated group while most low-score rejects (20) are never read by a person.
- **Refusing.** Refusing minors and missing consent protects people and follows the Law, but a refused applicant gets no decision at all and no human reading. My rules refused 0 of the 400 invented clean applicants; a real name with a digit or a symbol could be refused wrongly, so before real use the rule needs testing on real names and the refused cases need a person.
- **The extra escalation rule.** I added one (decision and score that contradict each other, or a reason that mentions a protected attribute). It fired 0 times. I kept it because it costs nothing, but it did not change any outcome in this run.
- **No counterfactual check.** I did not use it. It would double the model calls (800 instead of 400) and, with the name already hidden, it has nothing left to swap.
- **Cost.** The harness needs no extra model calls (400 and 400, 1.00 per profile). The real price is the work of the reviewers and the decisions that moved for reasons that are not a protected attribute.
- **Limits of the evidence.** One model, greedy decoding, invented data, 40 pairs per attribute (an effect smaller than 6 pairs of 40 cannot be seen), and I did not measure sensitivity to the order of the lines.

## AI-use declaration

I used Claude (Anthropic's chat assistant) in this assignment. It explained the task and the notebook step by step.
