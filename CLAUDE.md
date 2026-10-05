# Primed

Mobile web app (iOS app later) that makes sure a high school student never walks into a test unprepared: snap your exam schedule, snap your notes, get a short quiz every day until the test. English, US market first.

Full plan (features, flows, schema, timeline): the "Primed: Pre-Build Docs" doc. This file is the short version.

## How to work with me

- Ask clarifying questions and list the files you plan to touch BEFORE writing code. Never guess at a requirement.
- Build in small steps. One feature slice per session, then I test it on my phone.
- Never add features that are not listed under "Scope". If something seems missing, ask.
- Commit to git after every working step with a clear message.
- Never use em dashes in UI copy or comments.

## Stack (web beta, now)

- React + Vite + TypeScript, hosted on Netlify (deploys on every push to main).
- Mobile first: designed for a phone screen in the browser; desktop only has to work.
- Supabase (supabase-js): Postgres, auth, storage, Edge Functions.
- AI runs ONLY in Supabase Edge Functions. No API keys, secrets or prompts in the frontend.
- Sign in: Supabase email one-time code, behind an auth layer that is easy to swap for Sign in with Apple later.
- Scanning: the phone camera via a file input (accept="image/*", capture="environment"). The image is uploaded to storage and sent to an Edge Function. No on-device text recognition on web.
- Reminders: email via Resend, scheduled from exam dates.
- No payments during the free beta.
- Analytics: PostHog, one event per onboarding step (added in the beta stage).

Later (after Nov 9): a native iOS app in Swift on the SAME Supabase backend and Edge Functions. Keep all business logic and AI on the server so only the screens need rebuilding.

## Scope (v1)

- F1 Scan exam schedule: photo in, exams out (subject, date, topic, confidence 0 to 1).
- F2 Confirm exams: editable list, uncertain dates (confidence < 0.8) highlighted, nothing saved until confirmed, manual add.
- F3 Add notes to an exam: up to 10 photos, text extracted and stored.
- F4 Study plan: material split across days left, one quiz per day.
- F5 Practice quiz: 10 to 15 multiple choice questions from the student's own notes, explanation after each answer, each question linked to its source note.
- F6 Home countdown: next exam, days left, today's task, one tap into the quiz.
- F7 Reminders: emails 7, 3 and 1 day before, plus a daily nudge at the chosen time.
- F8 Onboarding (14 screens, ends in a real scan). Paywall comes after the beta.

Out of v1: flashcards, summaries, mind maps, tutor chat, grade tracking, calendar or school imports, native Android, social, dark mode, payments (until after the beta).

## Edge Functions

- extract-exams: photo in, list of exams out (subject, date, topic, confidence). Resolve dates against today and the user's time zone.
- extract-notes: photo in, clean text out, linked to one exam.
- generate-quiz: an exam's notes + today's slice of the plan in, 10 to 15 questions out (prompt, 4 options, correct index, one-line explanation, source material id).

## Data (Supabase)

Tables: profiles, exams, materials, plan_days, quizzes, questions, answers. Every row has user_id. Row-level security on every table: users only see their own rows. Private storage bucket, files under the user's id folder. Raw photos deleted 30 days after extraction. Store no real name, birthdate or school name; email only for login and reminders. Account deletion removes everything.

## Unhappy paths (always handle)

Blurry or empty photo (Retake / Type it in), no dates found (manual entry), uncertain date (highlight, tap to accept), date in the past (flag), no exams (empty state with scan button), quiz generation fails (retry, keep data), offline (show a clear message, keep what is cached), exam date changed (rebuild plan), reminder emails unsubscribed (banner on Home).

## Design

Bold and energetic, light theme only. System font stack (SF Pro on iPhone).

- Ink #111111, background #FFFFFF, surface #F4F4F5, quiet text #71717A
- Accent signal orange #FF5A1F: countdown number, primary quiz button, progress bars only
- Correct #16A34A, wrong #DC2626
- Countdown 72px heavy, titles 28px bold, onboarding questions 26px bold, body 17px, captions 13px
- Primary button: full width, 56px tall, black with white text
- Cards: 16px radius, 24px side margins, 8px spacing grid
- Copy: short and direct, like a coach ("Bio in 6 days. 12 minutes today.")

## Current stage

Stage 1, core loop. First task: F1 step 1 (a "Scan schedule" button that opens the phone camera, uploads the photo to Supabase storage, and shows it back on screen). No AI yet.
