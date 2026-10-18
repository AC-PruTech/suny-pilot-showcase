# SUNY Pilot — Marketing Cloud Email Templates (sample)

Fictional prototype templates for **SUNY Pilot** community (Education + Commerce on Experience Cloud — **ONE community on a CUSTOM LWR template** (single custom LWR community; 64 campuses via campus filtering + audience targeting; guest + authenticated in same site; LWC-only, SLDS 2; **not an LMS**); one Marketing Cloud account, **a business unit per campus**). Brand: deep navy `#102a43`, gold `#f5c542`, SUNY Pilot. **All data is sample data.**

## Files
- `ce-lead-welcome.html`, `ce-course-fit.html`, `ce-enrollment-reminder.html` — CE Lead Nurture
- `ce-enrollment-confirmation.html`, `ce-cohort-reminder.html`, `ce-certificate-earned.html` — CE Enrollment Onboarding
- `ce-waitlist-seat-open.html` — Cohort Fill / Waitlist
- `employer-sponsor-packet.html` — Employer-Sponsored CE
- `journey-email-map.md` — journey spec: entry, audience, sequence/wait/decision logic, exit/goal, BU-per-campus notes, data fields (incl. the secondary For-Credit journey)
- `screenshots/` — validation renders (where generated)

## How to use
These are **standalone, email-safe HTML starting points to rebuild in Marketing Cloud Content Builder** — not production sends. Do not send from these files as-is.

1. Recreate each file as a Content Builder template/content blocks (header, body, single CTA, campus footer), keeping the table layout, inline CSS, 600px width and mobile styles.
2. Wire every `%%FieldName%%` AMPscript-style placeholder to your Education / Commerce / CRM data via your chosen Marketing Cloud data extensions and connector setup — described generically here on purpose; confirm exact object/field and connector details in your own org before building. Provide a fallback for every field.
3. Replace all `#` placeholder links: CTA → Experience Cloud Discover / learner home / Commerce checkout; footer → real unsubscribe, preference-center and view-in-browser links for the sending campus business unit.
4. Create the **plain-text version** from the `PLAIN-TEXT VERSION` comment block in each file and send multipart (HTML + text).
5. Set preheaders/subjects from each file's header comment; test per campus BU (footer address, phone, support/instructor contacts differ by BU).
6. QA before send: seed-test across major email clients and mobile widths, check dark-mode rendering, link/merge-field proofing, suppression & consent, and accessibility (alt text on any images added, heading order, contrast — navy on white / navy on gold).

Key content rules baked in: non-credit courses are flat-fee CEU courses, do **not** count toward degree credit, and are **not aid-eligible** (PSS aid is degree-only; employer/workforce funding is separate); the community portal is **not an LMS** (no course-player link — access/materials are emailed within 1 business day and the instructor contacts the learner).

**No emails were sent in creating these templates.**


> **Rebrand note:** Templates rebrand to SUNY Pilot; merge fields unchanged. Pilot sample data, not official SUNY.
