# Access Controls, Tested Like Test Cases

I don't trust a control until I've seen it work. A policy that says "access is removed when someone leaves" proves nothing. A test that checks every system for that person's account does.

I look at controls with two sets of eyes. As a QA leader, I ask: does it work, and can I reproduce the result? As a security management graduate studying for the CISA, I ask: does it meet the standard, and can we prove it? The critical eye is the same. The question is different.

So I write security controls the way I write test cases: a clear condition, a clear action, a clear expected result, and the evidence that proves it. Each control below is mapped to NIST SP 800-53 Rev. 5. Where I've done this work for real, I added what I learned.

**The setting:** a fictional 200-person healthcare software company with an HR system, Microsoft 365, a cloud-hosted product, and a ticketing system. No real company data is used.

---

## 1. Access is removed when someone leaves
**Framework:** NIST SP 800-53 AC-2 (Account Management)
**Risk if it fails:** a former employee can still log in to company systems or patient data.

> **Given** an employee's last day was 3 days ago
> **When** I compare the HR termination list against active accounts in every system
> **Then** their account is disabled in all of them, and none of their sessions are still open

**Evidence:** HR termination report; active-user exports from each system; session sign-out records; device lock records; the offboarding tickets.
**Pass/Fail:** fail if any account or session is still active more than 24 hours after the last day.

**From experience:** When I know ahead of time, I get everything ready so I can act the moment the person is told. First I turn off single sign-on, email, and cloud storage. Then I end every open session. A disabled password doesn't help if a token is still live. I lock company laptops and tablets remotely and send a prepaid label to get them back. I move the mailbox to a shared one so nothing is lost. For the next few days, I watch the cloud drives for downloads, especially client files. If the person was a lead, I change any shared credentials they knew. I also keep a list of who takes over each person's email, calendar, and files. I confirm it with their manager before I hand anything over.

## 2. Access is reviewed every quarter
**Framework:** AC-2 (Account Management, account review)
**Risk if it fails:** people keep access they no longer need after changing jobs, and nobody notices.

> **Given** the quarterly access review is due
> **When** I pull a sample of 25 users and their permissions
> **Then** each one has a manager sign-off from this quarter, and every removal they requested has been made

**Evidence:** signed review records with dates; before-and-after permission lists for the removals.
**Pass/Fail:** fail if any sampled user lacks a current sign-off, or if a requested removal wasn't made.

## 3. Admin rights go only to people who need them
**Framework:** AC-6 (Least Privilege)
**Risk if it fails:** one compromised account can change or delete anything.

> **Given** the list of accounts with admin rights in each system
> **When** I match each one to an approved request and a job role that needs it
> **Then** every admin account has both, and no admin uses that account for everyday work

**Evidence:** admin account list; approved access requests; separate admin and everyday accounts for each admin.
**Pass/Fail:** fail if any admin account has no approval or no business need.

**From experience:** I once found an executive with top-level admin rights in both of our cloud platforms. She only needed to see the accounts, not run them. Then she forgot her password, and her reset attempt nearly locked the whole company out. Executives are public, so they're top targets for phishing. Their accounts should have only the access they need, not the most. I changed it right away.

## 4. Remote access requires multi-factor authentication
**Framework:** IA-2 (Identification and Authentication), AC-17 (Remote Access)
**Risk if it fails:** a stolen password alone is enough to get in from anywhere.

> **Given** a test user with a correct password
> **When** they sign in to email and the VPN from outside the office
> **Then** they can't get in without completing a second factor

**Evidence:** MFA policy settings; sign-in logs showing the MFA challenge; screenshots from the test.
**Pass/Fail:** fail if any remote sign-in path works with a password alone.

**From experience:** If a small business asked me which control to fix first, I'd pick this one. Enforce MFA, first and always. Stolen passwords are one of the most common ways in. Your email holds the keys to everything else, and it lives on your phone. MFA is cheap, too. Nobody has to spend thousands on a product. When we were a team of four, MFA was off. As we grew, I made it mandatory. Set it up right, too. One driver's MFA codes went by text to a single phone. When that phone broke, he couldn't sign in on any other device, and his work orders stalled until I fixed it. Every account should have a primary and a backup way to verify. My close second: list everyone with owner or super admin rights, and take it away from anyone who doesn't need it.

## 5. Accounts lock after repeated failed sign-ins
**Framework:** AC-7 (Unsuccessful Logon Attempts)
**Risk if it fails:** an attacker can guess passwords forever.

> **Given** the policy allows 5 failed attempts
> **When** I enter a wrong password 6 times on a test account
> **Then** the account locks, and the lockout appears in the security log

**Evidence:** lockout policy settings; the test result; the log entry.
**Pass/Fail:** fail if the account doesn't lock or the lockout isn't logged.

## 6. Passwords meet the standard
**Framework:** IA-5 (Authenticator Management)
**Risk if it fails:** weak or reused passwords get guessed or reused from other breaches.

> **Given** the password policy
> **When** I try to set a short password, a common password, and the previous password on a test account
> **Then** the system rejects all three

**Evidence:** password policy settings; the test results.
**Pass/Fail:** fail if any of the three is accepted.

## 7. Shared accounts are controlled
**Framework:** AC-2 (Account Management), IA-2 (Identification and Authentication)
**Risk if it fails:** when something goes wrong, nobody can tell who did it.

> **Given** the list of shared or generic accounts
> **When** I check each one for an owner, an approved reason, and a password that changes when someone leaves the team
> **Then** every shared account has all three, or it has been replaced with named accounts

**Evidence:** shared account list with owners; approvals; password change history.
**Pass/Fail:** fail if any shared account has no owner or no approved reason.

**From experience:** Early on, we didn't have separate test accounts. QA teams signed in to production with my email. When an automated test broke something, everyone looked at me. I worked with IT to lock down my own account. When named accounts rolled out, I worked with the platform team to test them. We checked that accounts were created, edited, and deleted correctly, including bulk imports, and that each one had the right access.

## 8. Security events are logged and reviewed
**Framework:** AU-2 (Event Logging), AU-6 (Audit Record Review, Analysis, and Reporting)
**Risk if it fails:** an attack happens and nobody sees it until it's too late.

> **Given** a test user who signs in, fails a sign-in, and changes a permission
> **When** I search the logs for those three events
> **Then** all three appear with the user, time, and system, and the log review for that week is signed off

**Evidence:** log search results; the weekly log review record.
**Pass/Fail:** fail if any event is missing or the review wasn't done.

## 9. Backups can actually be restored
**Framework:** CP-9 (System Backup)
**Risk if it fails:** the backup exists, but it doesn't work on the day you need it.

> **Given** last night's backup of a test database
> **When** I restore it to a separate environment
> **Then** the restore finishes, and a sample of records matches production

**Evidence:** backup job logs; restore test record with date, time taken, and who ran it.
**Pass/Fail:** fail if the restore fails, takes longer than the recovery target, or the data doesn't match.

## 10. Patient records are visible only to the care team
**Framework:** AC-3 (Access Enforcement), AC-19 (Access Control for Mobile Devices)
**Risk if it fails:** a clinician sees a patient they don't treat, or someone opens health records on a device the company never approved.

> **Given** Patient A was seen by Doctor A
> **When** Doctor A adds notes, and I sign in as Doctor B, who has no access to Patient A
> **Then** Doctor B can't see any of Patient A's health information

> **Given** a user with a valid account
> **When** they sign in from a device that isn't approved
> **Then** access is denied

**Evidence:** the role permission list; test results for every role; screenshots; access logs.
**Pass/Fail:** fail if any role sees records outside its permissions, or any unapproved device gets in.

**From experience:** I ran this test on a healthcare AI project, once for every role: doctor, nurse practitioner, nurse, and physician assistant. For the device check, I used a device cloud like BrowserStack to sign in from devices that weren't approved.

---

## What I've learned beyond these ten

**Know all the personal data.** Personal data is more than a name, birth date, and Social Security number. On a school product with UK users, we had to protect minors' health records, religion, custody agreements, sexual orientation, addresses, phone numbers, and IP addresses. That data touched so many systems that tracing it for the privacy impact assessment was the hardest part. Don't assume a control protects the data because it's supposed to. Test it. Confirm how it works, and that it does.

**QA and audit ask different questions.** Take a payment page. QA asks: does an invalid card number still go through, and can I reproduce it? An auditor asks: does this meet PCI DSS, is the card data encrypted at rest and in transit, and can we prove it? QA weighs an edge case by how likely it is. Would anyone really enter a 100-digit card number? An auditor treats every exception as a possible gap. I use both lenses.

**Evidence only counts if you handle it right.** My audit and forensics classes drove this home. Break the chain of custody, and the evidence might as well not exist. One missed step can decide whether a case holds up.

**Some control families take patience.** When I mapped all 20 NIST 800-53 control families in a GRC bootcamp, System and Communications Protection was the hardest. It has a lot of base controls and many variations, covering encryption, secure communications, and network boundaries. Supply Chain Risk Management came next. You have to understand each vendor's risk before you can pick the right controls.

## How I'd use this
- **Before an audit:** run every test, collect the evidence, and fix failures before the auditor finds them.
- **After a change:** rerun the tests a new system or process touches, the same way I'd rerun regression tests after a release.
- **For trends:** track pass/fail each quarter. A control that keeps failing needs a better design, not more reminders.

---

*Drafted with Claude Code, then edited by me.*
