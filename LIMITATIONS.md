# Limitations

Honest limits of this dataset. Read before citing it.

## 1. n = 1 operation

One operator, one coordinator, one docket, one round, four seats. Every
procedural choice — the docket brief, the field assignments, the synthesis —
passes through a single human's judgment. There is no inter-operator
reliability measure, and the protocol has not been replicated by an
independent party.

## 2. Seats are commercial black boxes

Seat labels name **products on specific dates, never model weights.**
The operator does not know which model served any given leg, and vendors
change models under stable product names without notice. A leg labeled
"ChatGPT, 2026-10-02" is a snapshot of that product's behavior that day; it
is not a measurement of any particular model and may not reproduce tomorrow.

## 3. No blinding beyond memory-free legs

The jury property is enforced procedurally (fresh sessions, identical brief,
no cross-exposure before filing). There is no technical blinding: seats share
training corpora and web sources, so **agreement between seats is not
independent evidence** in the statistical sense. Correlated errors are
expected. The dataset preserves the observed/inferred/speculating separation
to help readers discount appropriately, but it cannot quantify dependence.

## 4. Operator and coordinator effects

The coordinator drafted the consolidated verdict and all packaging; the
operator reviewed the docket brief. Framing effects from the brief's wording,
the choice of field assignments, and the synthesis are all present in the
record and cannot be separated out after the fact.

## 5. Docket selection

One docket on one thesis ("Memory as a Security Boundary"). The seats were
not tested on topics outside it. Nothing in this dataset speaks to how the
protocol performs on other questions.

## 6. English only; single-day snapshot

All material is in English, collected on 2026-10-02. Two slated seats
(DeepSeek, Mistral Small 4) had not filed at packaging time.

## 7. Seat claims are unverified by the authors

The coordinator recorded fidelity (what each seat said, including explicit
refusals to invent unverifiable details) but did not independently verify
seat citations against primary sources. Several seats' source lists should be
treated as leads, not as validated references. Known example: one seat's
"90 sources" were web searches run during the leg, not 90 inspected
documents.

## 8. The "first of its kind" claim is the authors' claim

The dataset is released under the authors' claim that no prior public
equivalent exists, supported by a commissioned originality study that found
no full public precedent (adversarial score: 3/10, "original operation,
unoriginal mechanics"). That study is not peer-reviewed, and absence of
evidence in a web search is not evidence of absence. Cite the claim as the
authors', not as established fact.
