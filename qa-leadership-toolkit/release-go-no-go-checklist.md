# Release Go/No-Go Checklist

I was the QA voice in the go/no-go call for every release for 11 years. I was never willing to trade quality for speed. A mistake costs far more to fix in production than it does to get right before release.

This checklist is how I made that call. It's short on purpose. If a box is unchecked, we talk about it before anyone deploys.

**When to use it:** at the go/no-go meeting before the release window. Run it again right before deployment if anything changed.

## 1. Scope is locked
- [ ] Release notes list every story and bug fix in this release
- [ ] Every item traces back to a requirement or ticket
- [ ] Nothing was added after code freeze without a Three Amigos call (QA, Dev, and Product) to justify it
- [ ] Work hidden behind a feature flag was **checked** to be hidden, including settings and text in the UI

## 2. Everything the release needs is listed
- [ ] Every service, job, and database change needed for this release is named, with an owner
- [ ] Changes from other teams going out in the same release are listed, and we know how they touch ours
- [ ] Certificates, keys, and licenses the release depends on are not about to expire

## 3. Testing is complete
- [ ] All planned test cases were run, and the results are recorded
- [ ] Automated regression suite passed, or every failure is explained
- [ ] Every fix was retested, along with the code around it, before its defect was closed
- [ ] Safety-critical and hardware checks were run, **even if their code wasn't touched**
- [ ] User acceptance testing signed off by the business owner

## 4. Open defects are acceptable
- [ ] **Zero open Critical or High defects**
- [ ] Every open Medium or Low defect has a known workaround and a target release
- [ ] Support knows what's shipping, especially fixes for the issues they get tickets about

## 5. Security and compliance
- [ ] Access changes reviewed: new roles, permissions, admin accounts
- [ ] No sensitive data in logs, test data, or error messages
- [ ] Privacy or regulatory impact reviewed where the release touches personal data
- [ ] AI-generated code reviewed and approved by a person before merge

## 6. The release can be undone
- [ ] Rollback plan written and **tested**
- [ ] Database changes can be reversed, or a backup is confirmed
- [ ] One person named to make the call on a mid-release rollback

## 7. The right people are there
- [ ] The release window is early enough that every team has someone available. No late Friday releases.
- [ ] Release-day roles assigned: deploy, QA validation, monitoring, communication
- [ ] Critical (P0) scripts ready to run the moment the release is live, with someone watching the logs
- [ ] Status reports scheduled for before, during, and after the release

## Decision

| | |
|---|---|
| **QA recommendation** | ☐ Go  ☐ No-Go  ☐ Go with conditions |
| **Conditions or reasons** | |
| **Time needed if No-Go** | |
| **Decided by** | |
| **Date** | |

**No-Go rule:** any unchecked box in section 2, 4, or 6 is an automatic No-Go, unless the release owner accepts the risk in writing.

## Lessons learned

**List every dependency, out loud, in the meeting.** We once split an email service out of a monolith and shipped it alongside another team's work. It passed every test in test and staging. In production it failed, because one service never got deployed. My critical scripts caught it within minutes. Since then, every service the release needs gets named in the go/no-go meeting.

**A short release still needs a full team.** We once shipped a big feature on a holiday evening, expecting it to take under an hour. An expired certificate took the portal down. Most managers were offline, and the people on the incident call didn't know a release was happening. We rolled back three or four times before we found the real cause. Now I release when every team has someone on hand, and I tell people early.

**Trust, but verify.** Developers once assured me a new feature was fully hidden behind a feature flag. Some settings still showed in the UI. Users weren't harmed, but I don't ship work I haven't checked. Now I verify the flag myself.

**There's no such thing as a "small" change.** Anything after code freeze is scope creep until QA, Dev, and Product agree it's worth the risk. If my scripts cover it, I'm more comfortable. If it's new and untested, I say so plainly.

**Bring facts, not feelings.** When I call No-Go, I bring the list of open defects, what was tested, the risk, and how much more time I need. A reason and a time frame turned "QA is slowing us down" into a decision people could make.

**Speak up early.** Don't save your concerns for the go/no-go meeting. It's cheaper to fix a problem before production, and nobody likes a surprise.

**Test what matters most, every time.** Some checks don't care what code changed. On a school safety product, I confirmed every release that the system flagged a sex offender at sign-in, in every environment. Children's safety was on the line.

---
