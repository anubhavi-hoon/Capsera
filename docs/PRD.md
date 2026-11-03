# CAPSERA — Product Requirements Document (PRD)

- **Document status:** Finalized MVP baseline
- **Version:** 1.0
- **Prepared:** 24 October 2026
- **Product:** CAPSERA — Digital Capacity Building and Learning Management Portal
- **Repository:** `capsera`
- **Problem statement:** Smart India Hackathon 2026, SIH26075
- **Primary context:** Ministry of Earth Sciences (MoES) / India Meteorological Department (IMD), Smart Education
- **Team:** Four members
- **Target delivery:** 2–4 weeks
- **Technology:** React + Vite, Node.js + Express, MySQL

This PRD is the single source of truth for product scope, behaviour, naming, acceptance criteria, API conventions, and team boundaries. Implementation details may be refined only through an agreed change to this document or a linked technical decision record.

---

## 1. Executive summary

CAPSERA is a responsive web portal for organizational capacity building. It centralizes professional profiles, training courses, learning resources, MCQ assessments, progress tracking, feedback, certificates, trainer competency mapping, and basic administrative oversight.

The product addresses a common organizational problem: training information, learning resources, participation, assessment results, and trainer expertise are often spread across separate tools or spreadsheets. CAPSERA connects those activities in one workflow and makes training participation and competency evidence easier to manage.

The MVP is deliberately competency-led rather than just a course catalogue. Users record skills and expertise; trainees discover suitable courses and trainers; training activity and assessment results are recorded; and the platform updates course progress and certificate eligibility.

The project is a student-built SIH solution, not an official government system. The MVP must not claim government approval, official certification authority, AI-powered matching, proctoring, or production-grade compliance certification unless those capabilities are separately implemented and validated.

## 2. Problem statement and context

The SIH26075 problem statement invites a centralized web-based platform for organizational training, competency development, and knowledge sharing. Its required capabilities include secure signup/login with Trainee, Trainer, and Admin roles; trainee profiles; course enrollment and learning resources; subject-wise MCQ assessments; feedback; trainer questionnaires and deadlines; trainer libraries; admin approval and role management; dashboards; homepage announcements; and competency-based trainer identification.

CAPSERA addresses these requirements through a role-based learning lifecycle:
1. Establish a trusted user identity and profile.
2. Define and discover courses, lessons, resources, skills, and trainer expertise.
3. Enroll trainees and record learning activity.
4. Assess learning with subject-wise MCQs and store results.
5. Track completion and issue a basic certificate when eligibility rules are met.
6. Give trainers and admins a consistent view of participation, outcomes, and competency matches.

## 3. Product goals

### 3.1 Goals for the MVP
- Provide a single responsive web application for trainees, trainers, and admins.
- Enforce server-side authentication, approval status, and role-based permissions.
- Let trainers create and publish courses, lessons, and learning resources.
- Let active trainees enroll in published courses and access permitted content.
- Support deadline-aware MCQ assessments with server-side scoring and persisted attempts.
- Track lesson completion, course progress, and assessment results.
- Match trainers to subjects/skills using an explainable, deterministic rule.
- Provide basic admin oversight of users, courses, enrollments, assessments, certificates, and announcements.
- Deliver an end-to-end demo using a real MySQL database, not only mock data or `localStorage`.
- Keep ownership and naming consistent across four parallel contributors.

### 3.2 Success criteria
The MVP is considered successful when all of the following are demonstrated:
- A new trainee can register, remain pending, and log in only after approval.
- An admin can approve accounts and assign allowed roles without allowing public self-assignment of Admin.
- A trainer can create and publish a course containing lessons and resources.
- A trainee can discover that course, enroll once, access allowed lessons/resources, and mark lessons complete.
- A trainee can attempt an assessment; the backend computes the score and stores the attempt and answers.
- The trainer who owns the course and an admin can view authorized result summaries; other trainees cannot.
- Progress and certificate eligibility follow the documented rules consistently.
- Trainer matching returns relevant trainers and explains why each was recommended.
- Admin counts and homepage announcements reflect database records.
- Core journeys work on mobile and desktop, and critical permission tests pass.

No numerical performance or impact claims (for example, percentage reductions in effort) should be made without measured evidence.

## 4. Users and roles

### 4.1 Trainee
A learner who completes a professional profile, declares skills and interests, discovers courses and trainers, enrolls in courses, studies resources, attempts assessments, reviews results, tracks progress, submits feedback, and views eligible certificates.

### 4.2 Trainer
A subject-matter expert who maintains an expertise profile, maps skills to a standard skill catalogue, creates courses and lessons, uploads learning resources, creates MCQ assessments with deadlines and attempt rules, and monitors participation and results for their own courses.

### 4.3 Admin
A platform administrator who approves accounts, assigns or changes roles, manages user status, oversees courses, views platform-wide summary metrics, publishes homepage announcements, manages the skill catalogue, and handles certificate/admin oversight.

### 4.4 Public visitor
A non-authenticated visitor can see the public landing page and published homepage announcements, and can open signup/login pages. Public visitors cannot access course materials, user profiles, results, dashboards, or private resources.

## 5. MVP scope

### 5.1 Included
- Authentication and role-based access.
- Admin approval of new accounts and role management.
- Trainee and trainer professional profiles.
- Skills catalogue and trainee/trainer skill mapping.
- Course creation, editing, publishing, discovery, and enrollment.
- Lessons and learning resource library (recorded lectures, presentations, documents, links).
- MCQ assessment creation, deadlines, attempts, server-side scoring, and stored results.
- Lesson completion and course progress tracking.
- Course feedback.
- Explainable skill-based trainer matching.
- Basic admin dashboard and summary statistics.
- Homepage announcements and achievements.
- Basic completion certificates with unique identifiers.
- Responsive layout, error states, validation, access controls, and integration tests.
- Deployment of the web app and database if the team’s environment and timeline permit.

### 5.2 Explicitly deferred
- Email/SMS/push notification service and notification inbox.
- AI-generated questions, adaptive testing, or ML-based recommendations.
- Proctored exams, camera monitoring, anti-cheat surveillance, device fingerprinting, or fullscreen enforcement.
- Advanced analytics, predictive skill-gap models, organizational heatmaps, and CSV/PDF reporting exports.
- Complex learning paths, course prerequisites, cohorts, calendars, live video conferencing, chat, discussion forums, and peer review.
- External LMS integrations or government identity systems.
- Native mobile applications and guaranteed offline learning.
- Multi-tenant organizational hierarchy, department-level workflows, and enterprise SSO.
- Cryptographic/tamper-proof certificates or claims of official accreditation.
- Payment, monetization, or public marketplace functionality.

These may be future enhancements, but must not be silently pulled into the MVP.

## 6. Core behaviour decisions

These decisions are the agreed defaults for the first release.

### 6.1 Registration, approval, and login
- Signup supports requesting a Trainee or Trainer account.
- All newly registered accounts start with status `PENDING`.
- Public signup must never accept `ADMIN` as a requested role. The backend validates the requested role against the allowed signup choices.
- An Admin account is provisioned through a deliberate seed/deployment process; there is no public admin signup.
- An admin approves or rejects pending users. The admin can change a user's role where permitted and can suspend/reactivate an account.
- Pending, rejected, or suspended accounts cannot access protected application functions.
- Passwords are hashed using a modern password-hashing library; plaintext passwords are never stored or logged.
- Login errors do not reveal whether an email address exists.
- Logout invalidates/clears the session according to the chosen auth implementation.
- The MVP includes profile editing and a basic password recovery approach. If email delivery cannot be configured in the student environment, recovery is handled by an admin-controlled reset process and documented as a demo limitation.
- Every protected API enforces authentication and authorization on the server, not only in the frontend.

### 6.2 Course lifecycle
- Trainers can create and edit their own courses in `DRAFT`.
- Trainers can publish their own courses after required fields are complete.
- Admins can review, unpublish, archive, or manage any course.
- Only `PUBLISHED` courses appear in the trainee course catalogue.
- A published course has a title, description/summary, subject, level, trainer, status, and optional thumbnail.
- Each course may contain ordered lessons and resources.
- Trainers may edit their own courses; users cannot modify another trainer's course unless they are Admin.
- Archiving is preferred over hard deletion after a course has enrollments, attempts, or certificates.

### 6.3 Enrollment
- Active trainees can enroll directly in published courses.
- Enrollment is unique per trainee and course. Duplicate enrollment is rejected or returns the existing enrollment consistently; the implementation must choose one behaviour and test it.
- Trainers and admins can view enrollment records for reporting, but cannot impersonate trainees.
- Trainees can view only their own enrollments and progress.
- A trainee may cancel an enrollment only if no business rule or completed record prevents it; completed learning history should be preserved.

### 6.4 Lessons and resources
- Lessons are ordered within a course.
- Trainers upload or link recorded lectures, presentations, PDFs/documents, and other approved study resources.
- Store uploaded files in a suitable storage service; store metadata and access references in MySQL. Large media binaries should not be stored in relational rows.
- Validate file size, allowed type, and upload permission on the server.
- Resource access follows course visibility and enrollment rules. A raw storage URL must not accidentally expose restricted content.
- For the MVP, trainees explicitly mark a lesson complete. The server validates that the lesson belongs to a course in which the trainee is enrolled.
- A completion event is persisted and updates course progress.

### 6.5 Assessment lifecycle
- A trainer creates an assessment within a course they own.
- An assessment includes title, instructions, question set, optional deadline, optional duration, maximum attempts, pass threshold, and status.
- Questions are four-option MCQs with one correct option, optional explanation, and points.
- Only published assessments are available to eligible trainees.
- A trainee must be enrolled in the associated course and have an active account.
- The default maximum is one attempt unless the trainer configures more. The backend enforces attempt limits.
- The deadline is optional; if set, the backend checks it at attempt creation and submission.
- The backend owns scoring. The client must not submit a trusted score, pass flag, or correctness value.
- Correct answers and answer-key metadata are not returned in the initial trainee assessment payload.
- After submission, the trainee sees their score, percentage, pass/fail status, and permitted review information.
- Each attempt and answer is stored with timestamps. Submission must be idempotent or reject a second submission consistently.
- Scoring and persistence occur in a database transaction to avoid partial results.
- Trainers can view attempts and aggregate results only for courses they own; Admins can view platform-wide results. Trainees can view only their own attempts.
- No proctoring or anti-cheat surveillance is included in the MVP.

### 6.6 Progress and course completion
- Lesson progress is recorded per enrollment and lesson.
- Course progress percentage is calculated from the number of completed lessons divided by the total number of lessons. If a course has no lessons, progress is 0%, not 100%.
- A course is complete when all lessons are complete and, if the course has a required published assessment, the trainee has passed it.
- If the course has no required assessment, completion is based on all lessons being completed.
- Assessment attempts and lesson progress remain separate evidence; an assessment pass alone does not imply course completion.
- Progress is derived from database records, not trusted from a client-provided percentage.

### 6.7 Certificates
- Basic certificates are issued when the completion rule above is met.
- Each certificate has a unique certificate code, trainee, course, issue date, and status.
- Duplicate certificates for the same trainee/course are prevented.
- A simple verification page may confirm whether a code exists and is valid, exposing only minimal public information.
- The MVP certificate is a platform-issued completion record, not a government-accredited or cryptographically tamper-proof credential.
- If time is tight, a basic certificate view and unique code are required; polished PDF design is secondary.

### 6.8 Competency mapping and trainer recommendations
- Skills are selected from an Admin-managed canonical skill catalogue; free-text interests can coexist but are not treated as normalized skills.
- Trainees can record skill proficiency and interests. Trainers can record expertise, proficiency, and years of experience.
- The recommendation engine is deterministic and explainable; no AI/ML service is required.
- Given a selected skill or subject, return active, approved trainers with matching mapped skills.
- Recommended MVP ranking:
  1. Exact skill match.
  2. Higher declared proficiency.
  3. More relevant experience.
- The response should explain matching factors, such as “matches Python; advanced proficiency; 3 years’ experience.”
- If no match exists, show an explicit empty state and allow the trainee to browse all published courses.
- The matching result is a recommendation, not a guarantee of trainer quality or a validated competency assessment.
- Skill-gap analysis may be shown as a simple comparison only when a target proficiency is explicitly defined. Do not invent an organizational competency score.

### 6.9 Course feedback
- Enrolled trainees can submit a 1–5 star course rating and optional comment.
- One current feedback record per trainee/course is allowed; the trainee may edit it.
- Trainers can view feedback for courses they own; Admins can view platform-wide feedback.
- Validate and limit comment length. Do not expose a trainee’s private profile fields in feedback reports.

### 6.10 Homepage announcements
- Admins can create, edit, publish, and archive announcements, achievements, and newly added learning content posts.
- Only published, non-expired announcements appear publicly on the homepage.
- Announcement fields include title, body, category, status, publication time, optional expiry, and creator.
- This is a homepage content feed, not a real-time notification system. Notification inboxes, email alerts, and push alerts are deferred.

### 6.11 Admin dashboard
The MVP dashboard provides database-backed counts for:
- Users by role and approval status.
- Published/draft courses.
- Enrollments and completions.
- Assessments and attempts.
- Certificates issued.
- Participation and assessment outcome summaries.

Dashboard counts must come from the database, not hard-coded demo numbers. Advanced trend charts, exports, cohort analytics, and predictive analysis are future scope. A few simple charts are optional if core flows are stable.

## 7. Functional requirements and acceptance criteria

Priority definitions: P0 = must work for MVP; P1 = included if core P0 flow is stable; P2 = future scope.

- **FR-01 — Account registration and login (P0)**
  - Users can register with name, email, password, and requested Trainee/Trainer role.
  - Email is unique; password policy and field validation are enforced server-side.
  - New accounts are `PENDING`.
  - Active approved users can log in and log out.
  - *Acceptance:* a pending account cannot access a protected route even if the frontend is manipulated.
- **FR-02 — Approval and role management (P0)**
  - Admin can list/filter pending users, approve/reject them, suspend/reactivate users, and assign permitted roles.
  - Public users cannot create or promote an Admin account.
  - *Acceptance:* an API call without Admin permission receives `403`; changes persist after refresh.
- **FR-03 — Profile management (P0)**
  - Trainee profile supports qualifications, work experience, interests, skills, certificates, biography, and organization/headline fields.
  - Trainer profile supports qualifications, work experience, subject expertise, skills, years of experience, biography, and organization/headline.
  - *Acceptance:* users can edit only their own profile; Admin access is governed by explicit permissions.
- **FR-04 — Skill catalogue and mapping (P0)**
  - Admin manages skill names/categories.
  - Trainees map skills and proficiency; trainers map expertise and proficiency.
  - *Acceptance:* duplicate skill mapping is prevented; recommendation query returns only approved active trainers.
- **FR-05 — Course management (P0)**
  - Trainer creates, edits, publishes, and archives their own courses.
  - Admin can manage all courses.
  - *Acceptance:* drafts are not visible in the trainee catalogue; ownership is validated by the backend.
- **FR-06 — Lessons and resources (P0)**
  - Trainer manages ordered lessons and attaches supported resources.
  - Trainee accesses resources only when entitled.
  - *Acceptance:* unauthorized users cannot retrieve restricted resource metadata or download URLs.
- **FR-07 — Enrollment (P0)**
  - Trainee enrolls in a published course and sees it in “My Learning.”
  - *Acceptance:* duplicate enrollment cannot create duplicate records; trainee cannot access another trainee’s enrollment.
- **FR-08 — Assessment creation and attempt (P0)**
  - Trainer creates questions and assessment settings; trainee attempts a published assessment.
  - Server enforces enrollment, deadlines, attempt limits, and scoring.
  - *Acceptance:* tampering with client-side score/correctness fields does not affect the stored result.
- **FR-09 — Results and progress (P0)**
  - Trainee sees own result and progress; course owner/Admin sees permitted reports.
  - *Acceptance:* lesson completion and assessment pass update course completion and certificate eligibility according to rules.
- **FR-10 — Feedback (P1, but planned for MVP)**
  - Enrolled trainee submits or edits a course rating/comment.
  - *Acceptance:* rating outside 1–5 is rejected; duplicate records are handled through edit/upsert semantics.
- **FR-11 — Certificates (P1, but planned for MVP)**
  - Eligible trainee receives a basic certificate record with unique code.
  - *Acceptance:* certificate cannot be issued before eligibility and duplicate issuance is prevented.
- **FR-12 — Announcements (P1, but planned for MVP)**
  - Admin publishes homepage announcements and achievements.
  - *Acceptance:* drafts and expired items are not shown to public visitors.
- **FR-13 — Trainer matching (P0)**
  - Trainee or Admin searches by skill/subject and sees explainable matches.
  - *Acceptance:* deterministic ordering is stable for equivalent data and empty results are handled.
- **FR-14 — Admin dashboard (P0)**
  - Admin views current counts and simple summaries.
  - *Acceptance:* numbers agree with seeded/test database records and are not hard-coded.
- **FR-15 — Responsive and accessible UI (P0)**
  - Primary flows work at mobile, tablet, and desktop widths.
  - Forms have labels, validation feedback, keyboard access, visible focus, and meaningful empty/error/loading states.
  - *Acceptance:* no essential action requires hover-only interaction or a desktop-only layout.

## 8. Non-functional requirements

### 8.1 Security
- Hash passwords using bcrypt or Argon2; never log passwords, tokens, secrets, or sensitive assessment answers.
- Use parameterized SQL queries and validate request bodies, path parameters, and query parameters.
- Enforce role and ownership checks in the backend on every protected operation.
- Use HTTPS in deployment and secure HttpOnly cookies or an equivalently secure, consistently implemented authentication design.
- If cookie-based authentication is used, configure CSRF protections for state-changing requests, strict CORS, SameSite cookie policy, and allowed origins.
- Apply login rate limiting, security headers, request size limits, and generic authentication errors before public deployment.
- Store secrets in environment variables; commit only `.env.example`.
- Validate uploads by size and allowed MIME/type; prevent executable uploads and path traversal.
- Do not expose correct MCQ answers before submission or another permitted review point.
- Record audit events for admin approval, role changes, suspension, and certificate issuance.
- Avoid collecting unnecessary personal data.

### 8.2 Privacy
- Trainees see their own profile, enrollments, attempts, and certificates.
- Trainers see learner records only where needed for courses they own.
- Admins have broader access for platform administration.
- Public certificate verification returns minimal data.
- Use synthetic/demo data in presentations; do not commit real personal information.

### 8.3 Reliability and data integrity
- MySQL is the source of truth for user, course, enrollment, assessment, progress, feedback, and certificate records.
- Use foreign keys, unique constraints, and indexes for integrity.
- Use transactions for assessment submission/scoring and certificate issuance where multiple writes must succeed together.
- Use consistent UTC timestamps in persistence and ISO 8601 timestamps in API responses.
- Prefer archive/status fields to hard deletion when historical records depend on a record.

### 8.4 Performance and scalability
- Paginate list endpoints that may grow.
- Index email, foreign keys, course status/subject, enrollment lookups, attempt history, and dashboard filters as justified by queries.
- Do not introduce Redis, message queues, microservices, or video transcoding for the MVP without a demonstrated need.
- Keep uploaded media in file/object storage rather than MySQL.

### 8.5 Accessibility and usability
- Responsive layouts for mobile, tablet, and desktop.
- Semantic headings, form labels, keyboard navigation, visible focus, readable contrast, and error messages associated with inputs.
- Do not rely on color alone to convey status.
- Use clear language for pending approvals, deadlines, attempt limits, scores, and completion rules.

## 9. Technical architecture

### 9.1 Approved stack
- **Frontend:** React + Vite, JavaScript.
- **Routing:** React Router.
- **HTTP client:** Axios or `fetch`; team must standardize on one client.
- **Backend:** Node.js + Express, JavaScript.
- **Database:** MySQL 8+ using InnoDB.
- **SQL access:** choose one approach centrally (recommended: `mysql2/promise` with parameterized queries, or an agreed ORM; do not mix access patterns across modules).
- **Validation:** use one agreed schema validation library or shared validation helpers.
- **File storage:** a cloud/object storage provider for deployed uploads; local storage may be used only for development if documented.
- **Testing:** API integration tests plus frontend critical-flow tests; exact test libraries are a team decision recorded before implementation.
- **Deployment:** static frontend hosting plus Node API hosting and managed MySQL, depending on free-tier availability.

### 9.2 Architecture
`Browser (React/Vite) → REST API (Node/Express) → MySQL`

The browser must not connect directly to MySQL. The API is responsible for authentication, authorization, validation, business rules, database transactions, and response formatting.

### 9.3 Proposed repository layout
```text
capsera/
├── client/
│   └── src/
│       ├── app/
│       ├── components/
│       ├── features/
│       │   ├── auth/
│       │   ├── profiles/
│       │   ├── courses/
│       │   ├── assessments/
│       │   ├── competency/
│       │   └── admin/
│       ├── layouts/
│       ├── services/
│       ├── hooks/
│       └── styles/
├── server/
│   └── src/
│       ├── config/
│       ├── middleware/
│       ├── modules/
│       │   ├── auth/
│       │   ├── users/
│       │   ├── profiles/
│       │   ├── courses/
│       │   ├── enrollments/
│       │   ├── assessments/
│       │   ├── competency/
│       │   ├── feedback/
│       │   ├── certificates/
│       │   ├── announcements/
│       │   └── admin/
│       ├── shared/
│       └── app.js
├── database/
│   ├── migrations/
│   └── seeds/
├── docs/
│   ├── PRD.md
│   ├── API_CONTRACT.md
│   ├── DATA_MODEL.md
│   └── DECISIONS.md
├── .env.example
├── .gitignore
└── README.md
```

## 10. Canonical naming registry

These names are mandatory unless the team approves a documented change.

### 10.1 Product and environment
- **Product name:** `CAPSERA`
- **Git repository:** `capsera`
- **Database name:** `capsera_db`
- **Frontend directory:** `client`
- **Backend directory:** `server`
- **API prefix:** `/api/v1`

### 10.2 Language and naming rules
- **JavaScript variables/functions:** `camelCase` (e.g. `traineeId`, `courseId`, `createAssessment`).
- **React components and class names:** `PascalCase` (e.g. `CourseCard`, `AssessmentResultPage`).
- **SQL tables and columns:** `snake_case`, plural table names (e.g. `users`, `course_id`, `created_at`).
- **JSON request/response fields:** `camelCase` (e.g. `courseId`, `createdAt`).
- **Environment variables/constants:** `UPPER_SNAKE_CASE` (e.g. `DB_HOST`, `JWT_SECRET`).
- **Enum/status values:** uppercase strings (e.g. `PENDING`, `PUBLISHED`).

Do not create synonyms for an existing canonical field. For example, use `traineeId`, not `studentId`, `learnerId`, and `userTraineeId` interchangeably.

### 10.3 Canonical roles and statuses
- **Roles:** `TRAINEE`, `TRAINER`, `ADMIN`
- **User statuses:** `PENDING`, `ACTIVE`, `REJECTED`, `SUSPENDED`
- **Course statuses:** `DRAFT`, `PUBLISHED`, `ARCHIVED`
- **Assessment statuses:** `DRAFT`, `PUBLISHED`, `CLOSED`
- **Enrollment statuses:** `ACTIVE`, `COMPLETED`, `CANCELLED`
- **Attempt statuses:** `IN_PROGRESS`, `SUBMITTED`, `GRADED`, `EXPIRED`
- **Resource types:** `VIDEO`, `PRESENTATION`, `DOCUMENT`, `LINK`, `OTHER`
- **Certificate statuses:** represented by `issued_at` and nullable `revoked_at`, or by one explicitly standardized status field. Do not mix both models without a decision record.

### 10.4 Canonical domain entities
- `User`
- `TraineeProfile`
- `TrainerProfile`
- `Skill`
- `TraineeSkill`
- `TrainerSkill`
- `Course`
- `Lesson`
- `LearningResource`
- `Enrollment`
- `LessonProgress`
- `Assessment`
- `Question`
- `AssessmentAttempt`
- `AttemptAnswer`
- `Feedback`
- `Certificate`
- `Announcement`

SQL tables use the plural `snake_case` equivalents (e.g. `assessment_attempts`). All members must use these names in documentation and API discussions.

## 11. Shared API conventions

### 11.1 General rules
- Base path: `/api/v1`.
- REST resources use plural nouns and consistent HTTP verbs.
- JSON is the default request/response format.
- API field names use `camelCase`.
- IDs are generated by the database and never trusted from public signup payloads for ownership or role.
- List endpoints use consistent pagination: `page` (default 1), `pageSize` (default 20, maximum 100).
- Dates are ISO 8601 strings in responses; all persisted timestamps are treated consistently as UTC.
- All non-public endpoints require active authentication and server-side role/ownership checks.

### 11.2 Response envelopes
Success:
```json
{
  "success": true,
  "data": {}
}
```

Error:
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "A human-readable message"
  }
}
```

Paginated list:
```json
{
  "success": true,
  "data": {
    "items": [],
    "pagination": {
      "page": 1,
      "pageSize": 20,
      "totalItems": 0,
      "totalPages": 0
    }
  }
}
```

### 11.3 HTTP status conventions
- `200` successful read/update/action.
- `201` created.
- `204` successful deletion/archive where no response body is needed.
- `400` validation error.
- `401` missing/invalid authentication.
- `403` authenticated but forbidden or account inactive.
- `404` resource not found.
- `409` duplicate/conflicting operation.
- `413` upload too large.
- `415` unsupported media type.
- `429` rate limited.
- `500` unexpected server error, with no SQL/internal secrets exposed.

### 11.4 Canonical endpoint map
**Auth and account administration:**
- `POST /api/v1/auth/signup`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/logout`
- `GET /api/v1/auth/me`
- `GET /api/v1/admin/users`
- `PATCH /api/v1/admin/users/:userId/status`
- `PATCH /api/v1/admin/users/:userId/role`

**Profiles and skills:**
- `GET /api/v1/profiles/me`
- `PUT /api/v1/profiles/me/trainee`
- `PUT /api/v1/profiles/me/trainer`
- `GET /api/v1/skills`
- `POST /api/v1/skills` (Admin)
- `PUT /api/v1/profiles/me/skills`
- `PUT /api/v1/profiles/me/trainer-skills`
- `GET /api/v1/trainers/recommendations?skillId=:skillId`

**Courses and learning:**
- `GET /api/v1/courses`
- `POST /api/v1/courses`
- `GET /api/v1/courses/:courseId`
- `PATCH /api/v1/courses/:courseId`
- `POST /api/v1/courses/:courseId/publish`
- `POST /api/v1/courses/:courseId/lessons`
- `PATCH /api/v1/lessons/:lessonId`
- `POST /api/v1/courses/:courseId/resources`
- `GET /api/v1/courses/:courseId/resources`
- `POST /api/v1/courses/:courseId/enrollments`
- `GET /api/v1/enrollments/me`
- `PATCH /api/v1/enrollments/:enrollmentId/progress`

**Assessments and feedback:**
- `POST /api/v1/courses/:courseId/assessments`
- `POST /api/v1/assessments/:assessmentId/questions`
- `POST /api/v1/assessments/:assessmentId/publish`
- `GET /api/v1/assessments/:assessmentId`
- `POST /api/v1/assessments/:assessmentId/attempts`
- `POST /api/v1/attempts/:attemptId/submit`
- `GET /api/v1/attempts/me`
- `GET /api/v1/courses/:courseId/results`
- `POST /api/v1/courses/:courseId/feedback`
- `GET /api/v1/courses/:courseId/feedback`

**Certificates, announcements, and admin:**
- `GET /api/v1/certificates/me`
- `GET /api/v1/certificates/verify/:certificateCode`
- `GET /api/v1/announcements`
- `POST /api/v1/announcements` (Admin)
- `PATCH /api/v1/announcements/:announcementId` (Admin)
- `GET /api/v1/admin/dashboard/summary`
- `GET /api/v1/admin/dashboard/assessment-performance`

This endpoint map is the shared contract target. Exact request fields, response examples, ownership rules, and error cases must be documented in `API_CONTRACT.md` before each module is implemented. Do not add alternate endpoints for the same operation without agreement.

## 12. Conceptual data model

The exact schema will be documented in `DATA_MODEL.md` and implemented as versioned migrations. The PRD requires these relationships:
- `users` has one optional trainee profile or trainer profile, depending on role.
- `users` (trainer) has many `courses`.
- `courses` has many `lessons`, `learning_resources`, and `assessments`.
- `users` (trainee) and `courses` are many-to-many through `enrollments`.
- `enrollments` has lesson completion records through `lesson_progress`.
- `assessments` has many `questions` and many `assessment_attempts`.
- `assessment_attempts` has answer rows through `attempt_answers`.
- `users` and `skills` are many-to-many through `trainee_skills` and `trainer_skills`.
- `feedback` links one trainee to one course; one current feedback record per trainee/course.
- `certificates` link a trainee and course, with a unique verification code.
- `announcements` reference their Admin creator.

Integrity requirements:
- Unique user email.
- Unique `(trainee_id, course_id)` enrollment.
- Unique `(assessment_id, trainee_id, attempt_number)` attempt.
- Unique `(trainee_id, course_id)` feedback and certificate where the MVP allows one each.
- Unique skill names and certificate codes.
- Foreign keys and indexes on relationship/filter columns.
- Database transactions for assessment submission and certificate issuance.
- Soft archive/status fields for important records with historical dependencies.

## 13. Four-member work allocation

- **Member 1 — Identity, profiles, and user governance:**
  Signup/login/logout/current user, authentication and authorization middleware, trainee/trainer profiles, admin approval and role/status management. Owns canonical `User`, `TraineeProfile`, `TrainerProfile` contracts. Shared responsibility for security review.
- **Member 2 — Courses and learning resources:**
  Course CRUD, publish/archive, lessons and resource library/upload metadata, course discovery, enrollment, lesson progress API integration. Owns `Course`, `Lesson`, `LearningResource`, `Enrollment`, `LessonProgress` implementation. Shared responsibility for end-to-end learning-flow testing.
- **Member 3 — Assessments, results, progress, and feedback:**
  Assessment creation/settings and MCQ question management, attempt lifecycle, scoring, result review, attempt history, progress rule integration and feedback. Owns `Assessment`, `Question`, `AssessmentAttempt`, `AttemptAnswer`, `Feedback` implementation. Shared responsibility for transactional integrity and assessment security tests.
- **Member 4 — Competency, certificates, announcements, and dashboard:**
  Skill catalogue and trainee/trainer skill mapping, explainable trainer recommendation logic, basic certificate issuance/verification, homepage announcements and admin summary metrics. Owns `Skill`, `TraineeSkill`, `TrainerSkill`, `Certificate`, `Announcement`, dashboard queries. Shared responsibility for system-level integration and demo readiness.

## 14. Change control and approval

This PRD is the finalized baseline for the MVP. Changes should be proposed through a short decision record containing:
1. Requirement/section being changed.
2. Reason and expected user value.
3. Scope/time impact.
4. Data/API compatibility impact.
5. Approval from all four members.

The shared API contract, naming registry, and database model must be updated alongside any approved change.

