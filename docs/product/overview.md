# Product overview

## Mission

**Practice machine learning into mastery.**

Katalume is the training ground for machine learning — solve real ML problems
in an in-browser judge, compete in contests, and climb to mastery. LeetCode
rigor meets Kaggle depth.

The name combines **kata**, deliberate practice that forges mastery, with
**lume**, light or illumination—the moment a hard problem clicks.

## Product pillars

1. **Practice:** structured ML/DS problems with sample and hidden test cases.
2. **Feedback:** immediate verdicts, runtime, memory, and submission history.
3. **Progress:** solved state, streaks, difficulty coverage, topic coverage, and
   leaderboards derived from real submissions.
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
- Run, submit, and submission history data paths
- Competition list/detail, registration, and leaderboard display
- Learning and interview tracks
- Profile and avatar editing
- Admin content CRUD
- Light/dark themes, responsive navigation, keyboard and accessibility work
- Sentry hooks, CI, unit tests, and Playwright tests

## Explicit non-claims

The current deployment must not be marketed as a fully live judge until P0
launch gates close. Billing, team/organization administration, email
verification, password recovery, and plagiarism detection are not complete
production capabilities.
