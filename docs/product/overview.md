# Product overview

## Mission

**Practice machine learning into mastery.**

Katalume is the training ground for machine learning — solve real ML problems
in an in-browser judge, compete in contests, and climb to mastery. LeetCode
rigor meets Kaggle depth.

The name combines **kata**, deliberate practice that forges mastery, with
**lume**, light or illumination—the moment a hard problem clicks.

## Product pillars

1. **Practice:** structured ML/DS problems with samples and difficulty-sized
   practice suites.
2. **Feedback:** immediate local verdicts, runtime, and device-local history.
3. **Progress:** solved state and difficulty/topic coverage; durable ranked
   progress follows with isolated server execution.
4. **Guidance:** curated learning and interview tracks connected to relevant
   problem sets.
5. **Competition:** timed contests, registration, and ranked standings.
6. **Operations:** administrator-authored problems, tracks, and contests.

## Primary personas

| Persona | Goal | Critical experience |
|---|---|---|
| Learner | Build applied ML skills | Discover → solve → understand feedback → progress |
| Interview candidate | Prepare efficiently | Filtered tracks, realistic constraints, measurable coverage |
| Competitor | Test speed and accuracy | Register, submit during a window, trust the standings |
| Content administrator | Operate the catalog | Create/edit/delete validated content with auditability |
| Platform operator | Keep execution safe and reliable | Observe, throttle, recover, and control infrastructure cost |

## Scope available today

- Public marketing landing page
- Login and signup
- Dashboard and progress views
- Problem catalog and Monaco-based solve arena
- Browser Run/Submit and device-local practice history
- Competition list/detail, registration, and leaderboard display
- Learning and interview tracks
- Profile and avatar editing
- Admin content CRUD
- Light/dark themes, responsive navigation, keyboard and accessibility work
- Sentry hooks, CI, unit tests, and Playwright tests

## Explicit non-claims

The current public release is a practice beta, not an adversarial ranked judge:
browser-delivered tests can be inspected and local history is device-specific.
Billing code is deployed dark and cannot charge until its commercial activation
gates are completed. Ranked contests, team/organization administration, email
verification, password recovery, and plagiarism detection are not complete
production capabilities.
