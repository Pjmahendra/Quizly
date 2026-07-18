# Product Requirements Document (PRD)

## Quisly – Interactive AI-Powered Quiz & Assessment Platform (Web)

**Version:** 1.1 (Draft — consolidated)
**Product Type:** Web Application
**Target Users:** Educational Institutions, Universities, Schools, Corporate Training Organizations

---

# 1. Product Overview

Quisly is a modern, web-based assessment platform that enables educational institutions and organizations to conduct interactive, secure, and scalable online assessments.

Unlike traditional quiz systems, Quisly focuses on:

* Real-time faculty-student interaction
* Live monitoring
* AI-assisted evaluation
* Automated grading
* Resource sharing
* Classroom collaboration
* Institution-level management

The platform is designed to support thousands of concurrent users while maintaining a responsive, real-time experience through WebSockets.

---

# 2. Product Vision

To become a complete digital assessment ecosystem where institutions can create, conduct, evaluate, monitor, and improve learning using modern web technologies and AI.

---

# 3. Product Scope

Quisly is a **web-only** application that provides a centralized assessment platform for educational institutions, colleges, schools, coaching centers, and organizations.

The product includes role-based portals, classroom management, quiz creation, real-time assessment, AI-powered evaluation, collaboration features, analytics, and institutional administration.

The platform will support the complete lifecycle of an assessment:

* User onboarding
* Institution management
* Classroom creation
* Quiz creation
* Live quiz execution
* Real-time monitoring
* Automated and AI-assisted evaluation
* Performance analytics
* Resource sharing
* Student collaboration

---

# 4. User Portals

## 4.1 Super Admin Portal

Responsible for managing the entire platform.

### Responsibilities

* Create/Edit/Delete Organizations
* Manage Colleges/Institutions
* Manage Organization Admins
* Global User Management
* Permission Management
* Role Management
* Subscription Management
* Platform Analytics
* Platform-wide Notifications
* Audit Logs
* Feature Toggles
* System Monitoring

---

## 4.2 Organization Admin Portal

Each college/organization has its own administrator.

### Responsibilities

* Manage Faculty
* Manage Students
* Create Departments
* Create Classrooms
* Assign Faculty
* Invite Users
* Approve Registrations
* Reset Passwords
* View Institutional Analytics
* Generate Reports

---

## 4.3 Faculty Portal

The primary portal for educators.

### Quiz Management

* Create Quiz
* Edit Quiz
* Clone Quiz
* Delete Quiz
* Schedule Quiz
* Publish Quiz
* Draft Support
* Question Bank
* Randomized Questions
* Shuffle Options

### Question Types

* Multiple Choice
* Multiple Select
* True/False
* Short Answer
* Long Descriptive Answer
* Numerical
* Fill in the Blank (Future)
* File Upload (Future)

### During Live Quiz

Faculty can:

* View live participant count
* Monitor every student in real time
* View question-wise progress
* See current question of every student
* Pause Quiz
* Resume Quiz
* End Quiz
* Extend Time
* Broadcast announcements
* Remove student
* Lock quiz
* Unlock quiz

### Student Monitoring

A persistent live connection is maintained between the faculty and all students during the quiz.

Faculty can:

* Select any student
* View current progress
* See answered questions
* Monitor completion percentage
* View remaining time
* Detect inactivity
* Track reconnect events

This communication is maintained through **Socket.IO**.

---

## 4.4 Student Portal

Students participate using a clean and distraction-free interface.

### Features

* Join Classroom
* Join Quiz
* Live Quiz Experience
* Real-time Leaderboard (optional)
* Review Results
* View Performance History
* Raise Result Disputes
* Raise Quiz Concerns
* Download Reports
* View Peer Profiles (subject to organization privacy policies)
* Notification Center
* Profile Management

---

# 5. Classroom Management

Faculty can create classrooms.

Inside each classroom:

* Announcements
* Resource Sharing
* Notes
* PDFs
* PPTs
* Videos
* Assignment Links
* Quiz History

Students enrolled in the classroom automatically receive updates.

---

# 6. Student Discussion Rooms

Students can independently create discussion rooms.

Purpose:

* Group Study
* Topic Discussions
* Peer Learning
* Collaborative Preparation

Features:

* Invite Students
* Chat
* Shared Resources
* Discussion Threads

Faculty are not mandatory participants but may join if invited.

---

# 7. Real-Time Quiz System

One of Quisly's primary differentiators.

When a quiz starts:

* Every participant establishes a live Socket.IO connection.
* Faculty dashboard updates instantly.
* Student progress is synchronized.
* Leaderboards refresh live.
* Timer synchronization occurs automatically.
* Connection recovery is supported.

Real-time events include:

* Student joined
* Student disconnected
* Student reconnected
* Question answered
* Quiz completed
* Faculty announcement
* Timer updates

---

# 8. Quiz Workflow

Faculty

↓

Create Quiz

↓

Schedule Quiz

↓

Invite Students

↓

Students Join

↓

Live Quiz Begins

↓

Faculty Monitors Progress

↓

Quiz Ends

↓

Results Generated

↓

AI Evaluation (Optional)

↓

Performance Analytics

---

# 9. File & Resource Management

Faculty can upload learning resources to their classrooms.

Supported resources include:

* PDF documents
* PowerPoint presentations
* Images
* Word documents
* External links
* Course notes
* Recorded lectures

Students can:

* View resources
* Download resources
* Bookmark resources

## Storage Strategy (MVP)

For the initial release, all uploaded files will be stored using **Azure Blob Storage Emulator (Azurite)** running locally during development.

To maintain scalability and organization, uploaded files will follow a structured folder hierarchy instead of storing all files in a single container.

Example storage layout:

```
storage/

└── organizations/
    ├── org-001/
    │
    ├── faculty/
    │   ├── faculty-101/
    │   ├── faculty-102/
    │
    ├── classrooms/
    │   ├── classroom-001/
    │
    ├── quizzes/
    │   ├── quiz-001/
    │   │   ├── resources/
    │   │   ├── attachments/
    │   │
    │   ├── quiz-002/
    │
    └── students/
        ├── profile-images/
        └── submissions/
```

This hierarchy provides:

* Organization-level isolation
* Easier backup and migration
* Better permission management
* Cleaner resource organization
* Straightforward transition to Azure Blob Storage in production

Future deployments can replace Azurite with Azure Blob Storage without requiring architectural changes.

---

# 10. Evaluation System

## Objective Questions

Automatically graded.

Supported:

* MCQ
* Multiple Select
* True/False
* Numerical

---

## Manual Evaluation

Faculty manually evaluate:

* Descriptive Answers
* Essays
* Long Answers

---

# 11. AI Evaluation (Phase 3)

> **AI Evaluation is planned as the final implementation phase of the product.**

This is the flagship feature of Quisly. AI-assisted evaluation focuses on descriptive and subjective responses while allowing faculty to retain full control over grading.

## Workflow

1. Faculty creates a quiz.
2. Faculty provides the expected answer(s) for each question.
3. Students complete the quiz.
4. Responses are stored.
5. Faculty opens an individual student submission.
6. Faculty selects **Evaluate with AI**.
7. AI evaluates the response using the configured grading rules.
8. Faculty reviews the suggested score, feedback, and reasoning.
9. Faculty may accept, modify, or override the evaluation before publishing.

## Supported Evaluation Types

* Descriptive answers
* Short answers
* Long-form answers
* Explanatory responses

## Configurable Evaluation Parameters

Faculty can configure evaluation behavior for each question, including:

* Fuzzy matching sensitivity
* Case sensitivity
* Semantic relevance
* Keyword importance
* Partial marking
* Strict or lenient grading
* Maximum score
* Custom grading instructions
* Rubric-based evaluation

Example instruction:

> *Award full marks if the student's explanation is conceptually correct, even if the wording differs. Ignore grammatical mistakes but deduct marks if essential concepts are missing.*

## AI Evaluation Scope (MVP)

For the initial release, AI evaluation will be performed **per individual student response**.

Bulk or batch AI evaluation is intentionally excluded from the MVP and may be introduced in a future release after evaluating performance and usage patterns. When batch evaluation is introduced, jobs will be processed asynchronously using a queue-based architecture:

* **BullMQ**
* Redis-backed job queues
* Batch evaluation
* Retry support
* Progress tracking
* Failure recovery

Faculty will be able to monitor:

* Pending Evaluations
* Completed Evaluations
* Failed Evaluations
* Queue Progress

Faculty retains complete control and may override AI-generated scores before publishing.

---

# 12. Live Quiz Monitoring

One of Quisly's core capabilities is providing faculty with live visibility into every student's assessment progress.

During an active quiz, every participant establishes a persistent **Socket.IO** connection with the server.

The faculty dashboard receives live updates including:

* Student joined
* Student disconnected
* Student reconnected
* Current question
* Questions answered
* Completion percentage
* Remaining time
* Quiz completion
* Live leaderboard updates (optional)

Faculty can select any student during the quiz to monitor their progress in real time.

---

# 13. Quiz Integrity & Activity Monitoring

To help maintain assessment integrity, Quisly includes lightweight browser activity monitoring during quizzes.

> **Scope statement:** Quisly is **not** a full remote proctoring solution. It does not perform
> webcam monitoring, screen recording, or eye tracking in the MVP. It provides browser activity
> monitoring, live event reporting, and audit logs — keeping the scope realistic while leaving room
> for future enhancements.

## Window Focus Detection

The system monitors browser visibility events during an active quiz.

If a student:

* Switches browser tabs
* Minimizes the browser
* Switches to another application
* Causes the quiz window to lose focus

the client immediately reports the activity through the active **Socket.IO** connection.

## Faculty Alerts

Whenever a focus-loss event occurs:

* A real-time toast notification is displayed on the faculty dashboard.
* The affected student's activity timeline is updated.
* The event is recorded in the quiz audit log.
* The student's submission is flagged for review.

Example notification:

> **Warning:** *Rahul Sharma switched away from the quiz window (Warning 2 of 3).*

## Warning Policy

Tab-switch detection is a **configurable proctoring policy**, not a hardcoded behavior — organizations can enable, disable, or adjust it per quiz.

By default:

* First focus loss → Warning #1
* Second focus loss → Warning #2
* Third focus loss → Automatic quiz termination

When the warning limit is reached:

* The student's quiz session is automatically ended.
* Remaining questions are submitted as unanswered.
* The submission is marked as **Auto Terminated due to Quiz Integrity Policy**.
* Faculty are notified immediately.
* The activity log is preserved for later review.

The warning threshold is configurable at the quiz level (default: **3 warnings**) so institutions can adapt the policy to their assessment requirements.

---

# 14. Notifications

Students receive notifications for:

* Quiz Scheduled
* Quiz Starting Soon
* Quiz Published
* Results Published
* Resource Added
* Classroom Announcement
* Discussion Replies

Faculty receive notifications for:

* Student Joined
* Quiz Completed
* Evaluation Finished
* Student Concern Raised

Emails are delivered using **Resend**.

---

# 15. Analytics

## Faculty

* Average Score
* Pass Rate
* Question Difficulty
* Student Progress
* Time Taken
* Performance Trends

## Organization

* Department Performance
* Faculty Activity
* Student Engagement
* Assessment Statistics

## Platform

* Active Users
* Active Institutions
* Concurrent Quizzes
* System Health

---

# 16. Non-Functional Requirements

### Performance

* Support high concurrent quiz participation
* Low-latency real-time communication
* Fast dashboard updates
* Efficient caching

### Scalability

Designed using modular services capable of horizontal scaling.

### Security

* Role-Based Access Control (RBAC)
* Secure Authentication
* JWT Sessions
* Encrypted Passwords
* Audit Logs
* Input Validation
* Rate Limiting
* CSRF Protection
* XSS Prevention
* SQL Injection Prevention

### Reliability

* Automatic reconnect
* Retry mechanisms
* Queue-based processing
* Graceful failure handling

---

# 17. Technology Stack

| Layer                   | Technology                                     |
| ----------------------- | ---------------------------------------------- |
| Frontend                | Next.js (TypeScript)                           |
| Styling                 | Tailwind CSS                                   |
| Backend                 | Next.js API Routes / Server Actions            |
| Database                | PostgreSQL                                     |
| ORM                     | Prisma                                         |
| Authentication          | Better Auth / Clerk (TBD)                      |
| Real-time Communication | Socket.IO                                      |
| Cache                   | Redis                                          |
| Queue Processing        | BullMQ                                         |
| Email Service           | Resend                                         |
| Validation              | Zod                                            |
| Deployment              | Vercel                                         |
| File Storage            | Azure Blob Storage (Azurite in development)    |
| Monitoring              | Sentry / OpenTelemetry (Recommended)           |

---

# 18. Development Phases

### Phase 1 – Core Platform

* Authentication & RBAC
* Admin and Organization portals
* Faculty and Student portals
* Classroom management
* Quiz creation and scheduling
* Objective question evaluation
* Basic analytics

### Phase 2 – Real-Time Experience

* Socket.IO integration
* Live quiz execution
* Faculty monitoring dashboard
* Live leaderboards
* Quiz integrity & activity monitoring
* Notifications
* Resource sharing
* Student discussion rooms
* Performance analytics

### Phase 3 – AI Assessment

* AI-powered descriptive evaluation
* Configurable grading rules
* Semantic answer comparison
* AI score review and override
* Advanced evaluation analytics
* Batch evaluation with BullMQ (post-MVP)

---

## Core Value Proposition

Quisly is not just another quiz platform—it is a comprehensive assessment ecosystem that combines institutional management, real-time classroom interaction, collaborative learning, automated grading, and configurable AI-assisted evaluation into a single, scalable web application. Its architecture is designed to support educational institutions from small classrooms to large universities while remaining extensible for future AI-driven capabilities.
