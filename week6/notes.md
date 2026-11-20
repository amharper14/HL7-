# Week 6 Notes: Policy Behind Patient Access

Review notes for the Week 6 build tasks (no written submission required). Sources were checked on 2026-11-19.

---

## Task 1: HIPAA, patients, and their apps

Source: [HHS: Your Rights Under HIPAA](https://www.hhs.gov/hipaa/for-individuals/guidance-materials-for-consumers/index.html)

**Who must follow HIPAA**

| Group | Examples |
|---|---|
| **Covered entities** | Health plans (insurers, HMOs, employer plans, Medicare, Medicaid); most providers that bill electronically (hospitals, clinics, doctors, pharmacies, dentists, nursing homes); health care clearinghouses. |
| **Business associates** | Outside companies handling health info *for* a covered entity: billing, IT, lawyers, accountants, record storage. Bound by contract, and must follow the Privacy Rule and Security Rule safeguards. |

**Who does NOT have to follow HIPAA:** life insurers, employers, workers' comp carriers, most schools, many state agencies, most law enforcement, many municipal offices.

**Patient rights (covered plans and providers must honor these)**
- See and get a copy of your health records (**right of access**)
- Have corrections added
- Get a notice of how your information is used and shared
- Give or withhold permission for certain uses (e.g., marketing)
- Request restrictions on use or disclosure
- Get a report of when and why your information was shared
- File a complaint with the provider, the plan, or HHS

Details on access timing, fees, and formats are on the linked "Your Medical Records" / "Right to Access" pages, not on this overview.

Source: [HHS: Health Information on Personal Phones and Apps](https://www.hhs.gov/hipaa/for-professionals/privacy/guidance/cell-phone-hipaa/index.html) (opening paragraphs only)

**The key distinction**

| Where the data is | Is HIPAA protecting it? |
|---|---|
| In the hospital's EHR or patient portal | **Yes.** Held by a covered entity. |
| In an app the hospital (or its business associate) provides | **Yes.** |
| In an independent consumer app the patient chose | **Generally no**, even though the data came from the hospital. |
| Stored on the patient's own phone | **Generally no.** |

- Once the patient moves records into an independent app, that app's own privacy policy and terms matter, not HIPAA.
- HHS warns that app/device data can be collected by others, used for targeted ads, or sold to data brokers.
- **Takeaway:** "Access your medical records!" in an app ad does not mean "HIPAA-protected." Ask *who provides the app*.

**Link to Week 5:** a SMART on FHIR app gets the patient's data through the provider's authorized connection. The right of access lets the patient send it there. What happens *after* it arrives depends on who runs the app.

---

## Task 2: Information blocking

Source: [ONC: Information Blocking](https://healthit.gov/information-blocking/)

**What it is:** a practice by an **actor** that is likely to interfere with access, exchange, or use of electronic health information (EHI), unless required by law or covered by an exception.

**Who it applies to (the "actors")**

| Actor | Knowledge standard |
|---|---|
| Health care providers | Must *know* the practice is **unreasonable** and likely to interfere. |
| Developers of certified health IT | Know **or should know** it is likely to interfere. |
| HINs / HIEs | Know **or should know** it is likely to interfere. |

Providers have the higher bar to be found in violation.

**Exceptions** (45 CFR Part 171)
- Categories visible on the page include **Preventing Harm**, **Privacy**, **Fees**, and **Manner**. (Full list: the diagram on the page and the eCFR.)
- Meeting an exception = **not** information blocking.
- Not meeting an exception ≠ automatically information blocking. It's judged **case by case**.

**Familiar scenario: a patient hits a barrier getting or sharing records**

| Situation | Possible reading |
|---|---|
| Delay while the provider verifies the requester's identity | Could be legitimate (privacy). |
| Withholding a specific note to prevent serious harm | Could fit Preventing Harm. |
| Offering the records in a different format than asked | Could fit Manner, if a reasonable alternative is offered. |
| Charging a reasonable cost-based fee | Could fit Fees. |
| Refusing to connect a patient's chosen app for no real reason | Looks like interference. Worth investigating. |

**Where to go:** claims are filed through ONC's Report Information Blocking Portal. HHS OIG investigates all actor types. ONC reviews claims against certified developers.

---

## Task 3: Kill the Clipboard through a real company

Source: [CMS: Kill the Clipboard](https://www.cms.gov/health-tech-ecosystem/early-adopters/kill-the-clipboard)

**The pledge in plain terms:** companies commit to letting patients pull records (from CMS Aligned Networks or personal health record apps) and share them with providers using a **QR code or digital health link built on FHIR**, and, where possible, to give patients visit records back in that format. The goal is to stop making patients rewrite their history on a clipboard.

**Early adopters (12):** Apple, b.well Connected Health, Citizen Health, CVS Health, **Fasten Health**, Flexpa, Google, NantHealth, Samsung, Sharecare, UnitedHealth Group, Zocdoc. A much longer list of other pledgees follows.

**Company explored: Fasten Health**

Sources: [CMS pledge demo page](https://www.cms.gov/initiatives/health-technology-ecosystem/overview/pledge-demo-showcase/fasten-health), [Fasten docs](https://docs.fastenhealth.com), [SMART App Gallery listing](https://apps.smarthealthit.org/app/fasten-health), [Fasten Connect quickstart](https://docs.connect.fastenhealth.com/quickstart)

| Video step | What Fasten shows |
|---|---|
| **Retrieve** | Patient picks a health system, signs in to that system's portal (SMART on FHIR / OAuth2), and Fasten pulls the records. CMS listing: access through CMS Aligned Networks with verified identity. |
| **Review** | Records consolidated in one dashboard: labs, medications, visits, trends. |
| **Share** | Smart Health Links and digital insurance cards at the point of care (CMS listing). |

**Two products, same idea**
- **Fasten On-Prem**: open-source, self-hosted personal health record. The patient runs it on their own hardware.
- **Fasten Connect**: an API for other companies' apps. Its quickstart shows an Epic-style patient portal sign-in screen and a bulk export returned as FHIR records (JSONL).

**Connections to earlier weeks**
- Week 3/4: what comes back is FHIR resources like our Patient and Observation files.
- Week 5: listed in the SMART App Gallery (Epic, Cerner, Athena, Allscripts). Sign-in follows the authorize → token flow we traced.
- Task 1 above: a self-hosted, patient-chosen app is outside HIPAA. Fasten's pitch is that the data stays in the patient's hands.

Source: [CMS Health Tech Ecosystem overview](https://www.cms.gov/initiatives/health-technology-ecosystem/overview)

**Voluntary, not required**
- CMS *invites* companies, networks, EHRs, app developers, and providers to **voluntarily** align around a shared data and access framework. CMS calls it "a movement, not a mandate."

| | Kill the Clipboard pledge | HIPAA right of access / information blocking rules |
|---|---|---|
| Type | Voluntary industry commitment | Legally binding regulation |
| Applies to | Companies that sign up | Covered entities / defined actors |
| If not followed | Reputational | Complaints, investigations, penalties |

---

## One-line summary

HIPAA gives patients the right to get their records. Information-blocking rules stop actors from improperly getting in the way. Kill the Clipboard is a voluntary pledge to make sharing those records easy (FHIR + SMART Health Links). Once records land in an independent app, HIPAA usually no longer protects them.
