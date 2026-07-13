# Data model

```mermaid
erDiagram
    USER ||--o{ SUBMISSION : creates
    USER ||--o{ SESSION : owns
    USER ||--o{ EVALUATION_JOB : queues
    USER ||--o{ AUDIT_EVENT : acts
    USER }o--o{ CONTEST : registers
    USER ||--o{ LEADERBOARD : ranks
    PROBLEM ||--o{ SUBMISSION : receives
    PROBLEM ||--o{ TESTCASE : owns
    CONTEST }o--o{ PROBLEM : includes
    CONTEST ||--o{ LEADERBOARD : has
    SUBMISSION ||--o| EVALUATION_JOB : evaluates

    USER {
      ObjectId id
      string username
      string email
      string passwordHash
      stringArray roles
      string avatarUrl
    }
    PROBLEM {
      ObjectId id
      string slug
      string title
      string difficulty
      stringArray tags
      string starterCode
      object editorial
      number testcaseVersion
      number contentVersion
    }
    TESTCASE {
      ObjectId id
      ObjectId problemId
      number version
      string input
      string expectedOutput
      boolean isPublic
      number timeLimit
      number memoryLimit
    }
    SUBMISSION {
      ObjectId id
      ObjectId userId
      ObjectId problemId
      ObjectId contestId
      string idempotencyKey
      string code
      number languageId
      string status
      number runtime
      number memory
    }
    SESSION {
      string sid
      ObjectId userId
      string refreshTokenHash
      date expiresAt
      date revokedAt
      string replacedBy
    }
    EVALUATION_JOB {
      ObjectId userId
      ObjectId submissionId
      string kind
      string status
      number attempts
      date lockedAt
      date expiresAt
    }
    AUDIT_EVENT {
      ObjectId actorId
      string action
      string targetType
      string targetId
      date expiresAt
    }
    CONTEST {
      ObjectId id
      date startTime
      date endTime
      ObjectIdArray problems
      ObjectIdArray participants
    }
    LEADERBOARD {
      ObjectId contestId
      ObjectId userId
      number score
      ObjectIdArray problemsSolved
      date lastSubmissionTime
    }
```

`LearningTrack` separately stores slug, title, description, tags, ordered lesson
content, and display order.

## Implemented production indexes

Migrations create, without dropping unknown indexes:

- Submission `{ userId: 1, createdAt: -1 }`
- Submission `{ userId: 1, problemId: 1, createdAt: -1 }`
- Submission unique partial `{ userId: 1, idempotencyKey: 1 }`
- Submission `{ status: 1, userId: 1, problemId: 1 }`
- EvaluationJob claim/status, TTL, and unique submission indexes
- Session user/revocation and TTL indexes
- Testcase `{ problemId: 1, version: 1, isPublic: 1 }`
- Contest `{ startTime: 1, endTime: 1 }`
- Leaderboard unique `{ contestId: 1, userId: 1 }`

## Lifecycle rules required

- Problem deletion removes testcases; submission-retention/soft-delete policy
  still requires owner approval.
- Contest deletion removes leaderboard rows.
- User deletion cascades Sessions, jobs, submissions, leaderboard rows and
  registrations while retaining pseudonymizable audit evidence.
- Submission retention: define how long source, stdout/stderr, and metrics remain.
- Backups: encrypted automated backups plus regular restore drills.
- Migrations: `npm run migrate`; content/testcases publish by optimistic version
  switch and seeds are versioned/idempotent.
