# SUNY Pilot — Marketing Cloud Journey × Email Map

> **Sample / prototype only.** SUNY Pilot is fictional (deep navy `#102a43` + gold `#f5c542`, SUNY Pilot). All names, fees, dates, IDs and addresses are sample data. Rebuild these templates in Marketing Cloud Content Builder before any real use — see `README.md`.
>
> **Platform context:** Salesforce higher-ed — Education + Commerce on an Experience Cloud community — **ONE community on a CUSTOM LWR template** (single custom LWR community; 64 campuses via campus filtering + audience targeting; guest prospect + authenticated learner in same site; LWC-only, SLDS 2; **not an LMS**), one Marketing Cloud account with **a business unit (BU) per campus**. Focus is **non-credit / continuing education (CE) first**: flat-fee CEU courses that do **not** count toward degree credit and are **not degree-aid-eligible**. Funding/payment is tracked **in PSS by record type**: degree aid = `Degree_Financial_Aid`, employer-paid CE = `Employer_Sponsored`, workforce = `State_Workforce_Funding` (generic PSS naming — confirm exact PSS object/API names in-org).

## Template inventory

> **Rebrand note:** Templates rebrand to SUNY Pilot (headers/footers updated by find/replace; merge fields `%%...%%` unchanged). Pilot sample data, not official SUNY: SUNY campus names are public; learners/prices fictional.

| # | File | Journey(s) | Single CTA (placeholder) | Key merge fields |
|---|---|---|---|---|
| 1 | `ce-lead-welcome.html` | CE Lead Nurture | Explore non-credit courses → Discover | `%%FirstName%%`, `%%InterestCategory%%`, `%%CampusName%%` |
| 2 | `ce-course-fit.html` | CE Lead Nurture | See courses in `%%InterestCategory%%` | `%%CourseName%%`, `%%CourseCode%%`, `%%CEUs%%`, `%%CourseFee%%` |
| 3 | `ce-enrollment-reminder.html` | CE Lead Nurture / Enrollment Onboarding (abandon) | Complete my enrollment | `%%CourseName%%`, `%%CourseFee%%`, `%%StartDate%%`, `%%DeliveryFormat%%` |
| 4 | `ce-enrollment-confirmation.html` | CE Enrollment Onboarding | Go to my learner home | `%%EnrollmentID%%`, `%%OrderID%%`, `%%ReceiptID%%`, `%%InstructorName%%` |
| 5 | `ce-cohort-reminder.html` | CE Enrollment Onboarding | View my enrollment details | `%%StartDate%%`, `%%SessionTime%%`, `%%Location%%`, `%%CampusName%%` |
| 6 | `ce-certificate-earned.html` | CE Enrollment Onboarding (post-completion) / Nurture cross-sell | Explore your next course | `%%CertificateID%%` (`CERT-CE-######`), `%%CEUsEarned%%`, `%%NextCourseName%%` |
| 7 | `ce-waitlist-seat-open.html` | Cohort Fill / Waitlist | Enroll now to claim this seat | `%%EnrollByDate%%`, `%%EnrollByTime%%`, `%%StartDate%%` |
| 8 | `employer-sponsor-packet.html` | Employer-Sponsored CE | View my sponsored enrollment | `%%EmployerName%%`, `%%SeatAllocation%%`, `%%ReportingCadence%%`, `%%EnrollmentID%%` |

Every template: table-based layout, inline CSS, 600px max-width, mobile media query, hidden preheader, one CTA button, campus-BU footer (`%%BusinessUnitName%%`, `%%CampusName%%`, `%%CampusAddress%%`), and `#` placeholder unsubscribe / preference / view-in-browser links. A plain-text version is embedded as an HTML comment in each file.

---

## Journey 1 — CE Lead Nurture

- **Entry source:** CE interest / lead form on the Experience Cloud community (Discover), webinar/event signup, or imported CE enquiry list — wired from the Education/CRM lead record. Re-entry: no (one nurture pass per interest episode; re-enter on a new category interest).
- **Audience:** Prospective CE learners; not yet enrolled in the recommended course. Suppress: current enrollees in the same course, unsubscribed, and degree-only prospects (route those to Journey 5).
- **Sequence:**
  1. **Day 0 — `ce-lead-welcome.html`** (on entry). Welcome, catalog CTA, non-credit/not-aid-eligible note.
  2. **Wait 3 days → decision: engaged?** (opened/clicked, or browsed a category)
     - *Yes / category known →* **Day 3–4 — `ce-course-fit.html`** with category recommendations (Healthcare / IT / Business / Trades examples).
     - *No engagement →* send `ce-course-fit.html` once with the declared `%%InterestCategory%%` (or most-browsed category), then extend the next wait.
  3. **Wait 4 days → decision: started browsing/cart or enrollment?**
     - *Browsed/started, not enrolled →* **`ce-enrollment-reminder.html`** (flat fee, CEUs, start-anytime/cohort date, not aid-eligible).
     - *No activity →* optional value/content touch (not in this template set — add in Content Builder if wanted), then final wait.
  4. **Wait 7 days → decision: enrolled?** If not, exit to a long-term quarterly CE newsletter audience (with consent) rather than sending indefinitely.
- **Exit / goal:** **Goal = CE enrollment** (exits immediately into Journey 2). Other exits: unsubscribe (global suppression), 2+ hard bounces, no engagement after the final wait, or degree-intent signal (hand off to Journey 5).
- **BU-per-campus note:** Send from the lead's preferred-campus BU (`%%CampusName%%` / `%%BusinessUnitName%%`); if no campus preference, use the central CE BU and set campus fields to the nearest campus. Honor BU-level unsubscribe/suppression per campus.
- **Data fields:** `FirstName`, `LastName`, `Email`, `InterestCategory`, `CampusName` (preferred), `BusinessUnitName`, `CourseName/CourseCode/CEUs/CourseFee/StartDate/DeliveryFormat` (recommendation + browsed course), browse/cart activity flags + timestamps, consent & subscription status.

## Journey 2 — CE Enrollment Onboarding

- **Entry source:** Completed CE enrollment / Commerce order for a non-credit course (transactional trigger; consent not required for the confirmation, required for any promotional cross-sell).
- **Audience:** Newly enrolled CE learners, including self-pay and (with Journey 4 overlay) employer-sponsored learners.
- **Sequence:**
  1. **Immediately — `ce-enrollment-confirmation.html`** (transactional). Order/enrollment/receipt refs; **access & materials emailed within 1 business day**; **instructor will contact the learner**; explicit **“this portal is not an LMS — no course-player link.”**
  2. **Wait until 3 days before `%%StartDate%%` (cohort courses only) → `ce-cohort-reminder.html`:** session date/time, campus/location, what to bring, instructor/support contact. *Start-anytime courses:* skip; optionally send a Day 2 “access arrived?” check (future template).
  3. **Decision (daily): completion recorded?**
     - *Complete →* **`ce-certificate-earned.html`**: `%%CertificateID%%` (`CERT-CE-######`), `%%CEUsEarned%%`, `%%CompletionDate%%`, next-course cross-sell (`%%NextCourseName%%`) — send cross-sell version only with marketing consent; otherwise a transactional certificate notice without promotion.
     - *Incomplete / dropped →* exit to advisor follow-up (task/case in CRM), not more automated email, after at most one reminder.
  4. **Abandon branch (feeds from Journey 1 or checkout):** started enrollment, no order within 24h → `ce-enrollment-reminder.html`; repeat once at +3 days max, then stop.
- **Exit / goal:** **Goal = course completion + certificate issued.** Exits: completion, withdrawal/refund, cohort end + 14 days with no completion, unsubscribe (transactional confirmation still sends; promotional stops).
- **BU-per-campus note:** Send from the **campus of enrollment's BU** — footer address/phone, instructor and support contacts must be that campus's. Cross-campus enrollments send one confirmation per enrollment, from its own BU.
- **Data fields:** `FirstName/LastName`, `CourseName/CourseCode`, `EnrollmentID`, `OrderID`, `ReceiptID`, `CourseFee`, `StartDate`, `SessionTime`, `DeliveryFormat`, `CampusName`, `Location`, `InstructorName/InstructorEmail`, `SupportEmail/SupportPhone`, `CertificateID`, `CEUsEarned`, `CompletionDate`, `NextCourseName`, completion/enrollment status.

## Journey 3 — Cohort Fill / Waitlist

- **Entry source:** Learner joins a cohort waitlist in the community/Commerce, or a cohort is flagged under-filled (fill campaign to matched leads).
- **Audience:** Waitlisted learners for a specific course/cohort, in waitlist order; secondarily, category-matched CE leads for under-filled cohorts (use `ce-course-fit.html` variant).
- **Sequence:**
  1. **On waitlist join:** confirmation of waitlist position (future/transactional template; this set starts at seat-open).
  2. **Trigger: seat opens → immediately `ce-waitlist-seat-open.html`** to the next learner(s) in order, with **`%%EnrollByDate%%` / `%%EnrollByTime%%` deadline**.
  3. **Wait until deadline → decision: enrolled?**
     - *Yes →* exit into Journey 2 (confirmation sends from there — do not duplicate).
     - *No →* release seat to next on waitlist (re-trigger step 2 for them); move non-responder to end of queue or remove after 2 missed offers (configurable).
  4. **Cohort full or cohort starts:** close the waitlist; send a “cohort full — next cohort / similar courses” notice (future template; can reuse `ce-course-fit.html`).
- **Exit / goal:** **Goal = waitlisted learner enrolls before deadline (cohort filled).** Exits: enrolled, deadline missed ×2, cohort start reached, learner removes themselves, unsubscribe.
- **BU-per-campus note:** Waitlists are per campus/cohort — send only from that campus's BU, and never offer a seat at Campus A from Campus B's BU. Seat-release timing/deadlines are set per BU.
- **Data fields:** `FirstName`, `CourseName/CourseCode`, `CohortID` (internal), `StartDate`, `DeliveryFormat`, waitlist position/join timestamp, `EnrollByDate/EnrollByTime`, `CourseFee`, `CEUs`, `CampusName/Location`, seats remaining.

## Journey 4 — Employer-Sponsored CE

- **Entry source:** Employer sponsorship agreement / sponsored-seat allocation created in CRM-Commerce (employer = standard Account; sponsored seats on standard Order/OrderItem where sensible); learner assigned a sponsored seat by the employer or CE team, and a **PSS state payment (`RecordType = Employer_Sponsored`)** is created as the payment system of record. (B2B agreement precedes any learner email.)
- **Audience:** Sponsored learners (primary), with a parallel copy/packet to the employer contact where the agreement requires it. Suppress sponsored learners from self-pay price promotions for the sponsored course.
- **Sequence:**
  1. **On seat assignment — `employer-sponsor-packet.html`** to the learner: allocation (`%%SeatAllocation%%`), course/start, **$0 learner due — paid by the employer via a PSS state payment, `RecordType = Employer_Sponsored`** (PSS payment record e.g. `PSS-PAY-2026-####` is the system of record for the employer's payment), **completion reporting** (enrollment, completion status, CEUs, certificate ID; cadence `%%ReportingCadence%%`). Create/authorize that PSS payment record on seat assignment; status flow Authorized → Invoiced → Paid.
  2. **Then Journey 2 steps apply** (confirmation details, cohort reminder) — from the enrolling campus's BU, with any “amount paid” language replaced by “sponsored — $0 due” for this audience.
  3. **Decision: completion recorded?**
     - *Complete →* certificate email to learner (Journey 2) + completion report to employer per `%%ReportingCadence%%`, sourced from the CE Enrollment linked to the **PSS payment record (`RecordType = Employer_Sponsored`, PSS-PAY-2026-####)** — that PSS record + CE Enrollment are the system of record for employer completion/invoice reporting (report itself is a CRM/Commerce deliverable, not this template set). Employer invoice is issued against the PSS payment record (Status: Authorized → Invoiced → Paid; State Payment Date stamped when Paid).
     - *Not started by `%%StartDate%%` − 3 days →* one nudge to learner + notice to employer contact so the seat can be reassigned (operational email — draft in Content Builder).
- **Exit / goal:** **Goal = sponsored learner completes and employer reporting is delivered.** Exits: completion + report sent, seat reassigned/withdrawn by employer, cohort end, employer agreement end.
- **BU-per-campus note:** The **enrolling campus's BU sends learner email**; employer-level reporting rolls up across BUs to the account owner — a learner must never receive another campus's footer/contacts. If an employer sponsors seats at multiple campuses, each learner's packet comes from their own campus BU.
- **Data fields:** `FirstName/LastName`, `EmployerName` (standard Account), `EmployerContactName/EmployerContactEmail`, `SeatAllocation`, `CourseName/CourseCode`, `StartDate`, `EnrollmentID`, `CourseFee` (for “paid by employer via PSS” line; learner due = $0), `ReportingCadence`, completion status, `CEUsEarned`, `CertificateID`, `CampusName/BusinessUnitName`, plus PSS payment fields: PSS payment record name/ID (`PSS-PAY-2026-####`), `RecordType = Employer_Sponsored`, payment Status (Authorized/Invoiced/Paid), State Payment Date, linked CE Enrollment + Order IDs.

## Journey 5 — For-Credit Secondary (degree pathway)

- **Entry source:** Degree-program enquiry/application activity in CRM, or a CE learner signalling degree intent (e.g., clicks degree content, asks an advisor). **Deliberately secondary to the CE-first journeys above.**
- **Audience:** For-credit prospects/applicants. Keep strictly separate from CE nurture audiences to avoid implying CE credit transfer or aid eligibility for non-credit courses.
- **Sequence (outline — no templates in this set):**
  1. Degree welcome / program-fit email (for-credit catalog, admissions steps) — *to be built separately; do not reuse CE templates without rewriting credit/aid language.*
  2. **Wait 5 days → decision: application started?** Started-not-submitted → application reminder; no start → program-content touch.
  3. Application milestones (submitted → under review → decision → enrollment) as transactional/status emails, then degree onboarding. **PSS degree-aid messaging (`RecordType = Degree_Financial_Aid`) belongs only here** and in degree contexts; employer-paid CE also lives in PSS but as `RecordType = Employer_Sponsored` (Journey 4) and must never be messaged as degree aid.
  4. CE cross-over decision: a CE completer showing degree intent may enter here **only** with explicit degree-interest signal/consent, and copy must state CEUs do not automatically count toward degree credit.
- **Exit / goal:** **Goal = degree application submitted / degree enrollment.** Exits: enrolled (to degree onboarding), admitted-not-enrolled after deadline, unsubscribe, no engagement after defined window.
- **BU-per-campus note:** Send from the BU of the campus offering the chosen degree program; application/aid content and contacts are campus- and program-specific.
- **Data fields:** `FirstName/LastName`, program of interest, campus, application ID/status + milestone dates, intake term, advisor name/contact, consent — plus (only on cross-over) prior `CourseName/CertificateID` for advisor context, never as a credit promise.

---

## Cross-journey rules

- **Suppression & consent:** Global + BU-level unsubscribe and preference center (`#` placeholders in templates) honored before every promotional send; transactional confirmation/certificate notices follow applicable law and platform policy. Frequency cap across journeys (sample: ≤3 promotional CE emails / 7 days per learner).
- **Priority on conflict:** Enrollment/Waitlist (transactional & deadline) > Employer-Sponsored > CE Lead Nurture > For-Credit Secondary marketing.
- **Identity & data hygiene:** Match on email + CRM person/lead ID; de-duplicate a person enrolled via both self-pay and sponsor paths (sponsor path wins for that course). All `%% %%` fields must have tested fallbacks (e.g., “there” for missing `%%FirstName%%` — configure in Content Builder/AMPscript).
- **Measurement (sample goals):** Nurture→enrollment rate, checkout-abandon recovery, waitlist claim-before-deadline rate, confirmation→completion rate, certificate→next-course rate, sponsored-seat utilization + reporting completion.
