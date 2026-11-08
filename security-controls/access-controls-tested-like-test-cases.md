# Access Controls, Tested Like Test Cases

<!-- EVA: rewrite this intro in your own words if you'd like. -->
I don't trust a control until I've seen it work. A policy that says "access is removed when someone leaves" proves nothing. A test that checks every system for that person's account does.

So I write security controls the way I write test cases: a clear condition, a clear action, a clear expected result, and the evidence that proves it. Each control below is mapped to NIST SP 800-53 Rev. 5.

**The setting:** a fictional 200-person healthcare software company with an HR system, Microsoft 365, a cloud-hosted product, and a ticketing system. No real company data is used.

---

## 1. Access is removed when someone leaves
**Framework:** NIST SP 800-53 AC-2 (Account Management)
**Risk if it fails:** a former employee can still log in to company systems or patient data.

> **Given** an employee's last day was 3 days ago
> **When** I compare the HR termination list against active accounts in every system
> **Then** their account is disabled in all of them

**Evidence:** HR termination report; active-user exports from each system; the offboarding tickets.
**Pass/Fail:** fail if any account is still active more than 24 hours after the last day.

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

## 4. Remote access requires multi-factor authentication
**Framework:** IA-2 (Identification and Authentication), AC-17 (Remote Access)
**Risk if it fails:** a stolen password alone is enough to get in from anywhere.

> **Given** a test user with a correct password
> **When** they sign in to email and the VPN from outside the office
> **Then** they can't get in without completing a second factor

**Evidence:** MFA policy settings; sign-in logs showing the MFA challenge; screenshots from the test.
**Pass/Fail:** fail if any remote sign-in path works with a password alone.

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

---

## How I'd use this
- **Before an audit:** run every test, collect the evidence, and fix failures before the auditor finds them.
- **After a change:** rerun the tests a new system or process touches, the same way I'd rerun regression tests after a release.
- **For trends:** track pass/fail each quarter. A control that keeps failing needs a better design, not more reminders.

---

*Drafted with Claude Code, then edited by me.*
