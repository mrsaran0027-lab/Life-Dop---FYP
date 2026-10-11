# LifeDrop – workflow rebuilt from the flowchart

## Replace these files in your project
`app.js`, `index.html` (adds the Firebase **Auth** SDK script), and publish `firestore.rules`. `style.css` is unchanged.

## Firebase Console (one-time)
1. Build → Authentication → Sign-in method → enable **Email/Password**.
2. Build → Firestore Database → Rules → paste `firestore.rules` → Publish.
3. Register one account per role (Patient, Donor, Doctor) in the app to test.

## Flowchart → code
| Flowchart | Where |
|---|---|
| Login / Register, Choose ONE role, account details + blood group | `renderAuthPage`, `submitAuth` |
| Authenticate (Firebase Authentication) | `signInWithEmailAndPassword` / `createUserWithEmailAndPassword` |
| Role locked for session/account | role saved in `users/{uid}`; sign-in refuses a mismatched role; rules block role changes; no role switcher |
| Patient: view only matching donors → check compatibility → send request | `renderPatientDashboard`, `bloodGroupCanDonate`, `sendDonorRequest` |
| Donor accepts / rejects → DONOR FOUND, or back to list | `respondToOffer`, `matchedCard` |
| Donor: view only matching requests → accept / reject → patient accepts / rejects → PATIENT MATCHED | `renderDonorDashboard`, `acceptPatientRequest`, `rejectPatientRequest`, `respondToOffer` |
| Share contact/location securely | phone + location stored in `matches/{id}/contact/*`, readable only after status = Accepted |
| Side nav: Doctor Appointment | `renderAppointments` (patient and donor) |
| Doctor: review requests → Approve / Reject / Pending → requester sees status | `renderDoctorDashboard`, `confirmDecision`, `markPending` |
| End: session ended | `appLogout` |

## Firestore collections
`users`, `patientRequests`, `donors`, `matches` (+ `contact` subcollection), `appointments`.

## Removed because they are not in the flowchart
Groups page, home hero/action cards, the 1–5 "simulate dispatch" tracker, localStorage fallback, and the demo default user.

## Known gaps
- Anyone can register as a Doctor. Before real use, add a `verified` flag set by an admin and require it in the rules.
- A matched donor stays "Matched" (no cancel/complete step in the flowchart).
- Tested here with a mocked DOM only (all screens render, incompatible groups are filtered, user text is escaped). It has not been run against live Firebase.
