# Week 5 Notes: Exploring SMART on FHIR

Review notes for the Week 5 build tasks. Values in Task 2 and Task 3 were checked against the live discovery document on 2026-11-19.

---

## Task 1: Finding your way around the SMART specification

**Versions are separate standards**
- **SMART App Launch 2.2.0** sets the rules for how an app connects and gets authorized.
- **FHIR R4** is the data standard (the FHIR version we are using).

**The two launch approaches**

| Situation | Workflow |
|---|---|
| John opens a health records app on his phone, then connects it to his provider. | **Standalone launch**: the app starts on its own. |
| A clinician opens an app from inside an existing EHR session. | **EHR launch**: the app starts from inside the EHR. |

- "Standalone" describes where the app starts. It still needs authorized access.
- An EHR launch also requires authorization, even though the user is already signed in to the EHR.

**Backend services** (e.g., the hospital's overnight reporting job)
- No person signs in each time it runs.
- The service authenticates itself with credentials approved in advance and gets an access token.
- No sign-in does **not** mean unrestricted access. It still needs authorization and a token.

---

## Task 2: Reading a real SMART discovery document

**Address**
- FHIR base URL: `https://api.mock.health/fhir`
- Discovery document: base URL + `/.well-known/smart-configuration`
- Link to Week 4: the browser is a client sending an HTTP **GET** and receiving JSON.

**The two connection addresses**

| Field | Value | What it does |
|---|---|---|
| `authorization_endpoint` | `https://api.mock.health/smart/authorize` | Where the app sends the browser to request authorization. **Used first.** |
| `token_endpoint` | `https://api.mock.health/smart/token` | Where the app exchanges the authorization code + code verifier for an access token. **Used second.** |

**Workflow and security terms**

| Field | Values | Meaning |
|---|---|---|
| `capabilities` | `launch-ehr`, `launch-standalone` | Server supports both launch approaches. |
| `code_challenge_methods_supported` | `S256` | Method for creating the PKCE code challenge. |
| `grant_types_supported` | `authorization_code` | Exchange an authorization code for an access token. |
| `grant_types_supported` | `refresh_token` | Use a refresh token to get a new access token. |
| `grant_types_supported` | `client_credentials` | An approved service gets a token without a user signing in. |

- These grant types are **different ways** to get a token, not three steps every app performs.

**PKCE in one line:** the *challenge* goes with the authorization request. The *verifier* goes with the later code exchange. The server checks that the verifier produces the earlier challenge.

**What you actually received:** configuration information only. No patient records, no access token, no completed authorization.

**Don't confuse:**
- **CapabilityStatement**: what FHIR data and operations the server supports.
- **SMART discovery document**: how to get authorized access.

---

## Task 3: Scopes and patient context

`scopes_supported` is an array (list) of scope strings the server advertises.

**Reading a scope left to right: `patient/Observation.rs`**

| Piece | Meaning |
|---|---|
| `patient/` | Access concerns the patient in context (the patient ID is supplied separately). |
| `Observation` | The FHIR resource type (e.g., the HbA1c Observation from Week 3). |
| `r` | Read a specific resource by its id. |
| `s` | Search for matching resources. |

**Permission letters:** `c` create · `r` read · `u` update · `d` delete · `s` search

| Scope | Meaning |
|---|---|
| `patient/Observation.rs` | Read + search Observations. **Best fit for John's graph app.** |
| `patient/Observation.cruds` | Also create, update, delete. More than a graph needs. |
| `patient/Patient.rs` | Read + search the in-context patient's Patient resource. |
| `launch/patient` | Request patient context in a standalone launch. |
| `openid`, `fhirUser` | Identity information about the signed-in user. |

- Observation covers more than labs, e.g., blood pressure and body weight.
- Older `.read` scopes correspond to `.rs`. Seeing both styles is expected.

**User vs. patient**
- John signs in to help manage his mother's care.
- **User** = John. **Patient in context** = his mother.
- **ID token** conveys who the user is. **Patient context** identifies her record. **Access token** is used to request the permitted data.

**Supported vs. requested vs. granted**

| Term | Meaning |
|---|---|
| Supported | The server lists the scope in its discovery document. |
| Requested | The app asks for the scope during authorization. |
| Granted | The server approves it and reports it in the token response. |

- Granted can be less than requested. The provider's access rules still apply.
- Finding a scope in the discovery document does **not** give your app that permission.

---

## Task 4: The SMART App Gallery

For each listing, check **Designed for**, **Application Type**, and **FHIR Compatibility**.

| App | What to look for |
|---|---|
| CommonHealth | Helping people collect and manage their health information. |
| ASCVD Risk Calculator | Using health data for a calculation and visualization. |
| CDS Connect: Pain Management Summary | Combining different clinical information into a dashboard. |

- **Same FHIR version?** No. The ASCVD Risk Calculator listing says DSTU2.
- Being a SMART app does not automatically mean it supports our R4 environment.

**Questions to ask about any app**
1. Who would use it?
2. What problem does it solve?
3. Which FHIR resources might it need? (These are informed guesses unless the listing names them.)
4. Does it read information, change information, or both?

**Worked example: a medication-tracking app**
- Users: patients.
- Problem: keeping track of what they take.
- Likely resources: Patient, MedicationRequest, MedicationStatement.
- Read-only, unless it lets users record doses.

**The five foundations every SMART app builds on**
1. Discover the provider's connection information (`.well-known/smart-configuration`).
2. Request appropriate access (scopes).
3. Obtain an access token through the relevant workflow.
4. Request permitted FHIR resources.
5. Present the information in a useful way.

The intended audience alone does not determine an app's launch mode or its actual permissions.
