# Policy IVR

A voice line that a Policyholder of a Canadian motor insurer calls to check, pay for, or cancel their Policy without speaking to a human.

## Language

### People

**Policyholder**:
The person named on a Policy who is the only kind of Caller the IVR serves.
_Avoid_: Customer, client, member, user

**Caller**:
Anyone on an active Call, before Verification establishes them as a Policyholder.
_Avoid_: User, customer

### Policies

**Policy**:
A motor insurance contract identified by a Policy Number and held by one Policyholder.
_Avoid_: Plan, contract, account

**Policy Number**:
The digits-only identifier the Caller speaks or keys in to name the Policy they are calling about.
_Avoid_: Account number, policy ID

**Policy Status**:
The lifecycle state of a Policy: exactly one of Active, Lapsed, or Cancelled.
_Avoid_: State, standing

**Lapsed**:
A Policy Status for a Policy that ended because a premium went unpaid.
_Avoid_: Expired, suspended

**Cancelled**:
A Policy Status for a Policy that was ended deliberately; via the IVR it takes effect immediately.
_Avoid_: Terminated, closed

### Calls

**Call**:
One phone session from connect to hang-up.
_Avoid_: Session, conversation

**Verification**:
Confirming that a Caller is the Policyholder by matching the Policy Number exactly and the first name closely (tolerating mis-heard spellings).
_Avoid_: Authentication, login, ID&V

**Intent**:
One thing the Caller wants done, recognised from free speech or a keypad choice: Pay Premium, Check Policy Status, Cancel Policy, or Other Information. A Call handles one Intent and offers one more ("anything else?") before ending.
_Avoid_: Request type, action, purpose

**Pay Premium**:
The Intent to settle the full Amount Due on an Active Policy using the Payment Method on File.
_Avoid_: Make payment, top up

**Check Policy Status**:
The Intent to hear the Policy Status read back.
_Avoid_: Policy inquiry, lookup

**Cancel Policy**:
The Intent to make an Active Policy Cancelled immediately, carried out only after Cancellation Confirmation.
_Avoid_: Terminate, close account

**Cancellation Confirmation**:
The Caller's explicit "yes" (or keypress) after the IVR reads back that cancellation is immediate and irreversible.
_Avoid_: Consent, approval

**Other Information**:
The Intent for anything outside the other three; for now the Call ends politely, later it transfers to an agent.
_Avoid_: General inquiry, misc

**Retry**:
One more prompt after the IVR fails to recognise an Intent or verify the Caller; Verification and Intent each allow 3 Retries before the Call ends.
_Avoid_: Reprompt, attempt

### Money

**Payment Method**:
How a premium is paid: Credit Card or PAD.
_Avoid_: Payment type, tender

**PAD**:
Pre-Authorized Debit — the Canadian bank-account debit the Policyholder has authorised.
_Avoid_: Direct debit, ACH, EFT

**Amount Due**:
The full premium currently owed on a Policy; a Caller cannot choose a partial amount.
_Avoid_: Balance, outstanding, bill

**Payment Method on File**:
The Credit Card or PAD already recorded against the Policy; the IVR never collects card or bank details by voice.
_Avoid_: Saved card, default payment

### Records

**Call Record**:
The stored account of a Call: its Intent, outcome, and a PII-masked transcript, with no audio.
_Avoid_: Call log, recording, history

**Policy History**:
The dated list of everything that happened to a Policy — payments made and Policy Status changes — each linked to the Call that caused it.
_Avoid_: Audit trail, activity log, timeline

**Admin Console**:
The internal web screen where the developer views Policies, their Policy History, and Call Records; it changes nothing except resetting seed data.
_Avoid_: Admin portal, dashboard, back office
