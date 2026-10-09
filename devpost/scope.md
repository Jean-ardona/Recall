---
doc: scope
status: approved
---

# QuizGen — Notes-to-Quiz App

Paste your study notes, pick a question count, get a multiple-choice quiz you can take right now.

## The Unique Kernel
The questions come from *your* text, not from general knowledge. That's the moment of trust: you paste a paragraph from your syllabus, and the first question card makes it obvious the AI actually read it. That relevance is what makes a student want to use it the night before an exam.

## Who It's For
A student the night before an exam who doesn't want to re-read 200 pages — they want to check what they actually remember. Today they either reread their notes passively, or they skip review entirely because making flashcards takes too long. This app gives them a quiz in seconds.

## The Core Loop
Open the app → paste study notes → pick 5, 10, or 15 questions → click Generate → answer one card at a time, see immediate right/wrong feedback after each → reach the results screen with a final score. That's the whole loop.

## Inspiration & Identity
Clean, professional, sober. The kind of app that looks like a developer made it with care, not like an AI generated the UI. One font. Real icons from a proper icon library (no emojis). No flashy gradients. Card-based quiz interaction — focused, never cluttered. The vibe is a well-made study tool you'd actually trust with your exam prep.

## Why This Matters to the Learner
"I have a hard time finishing projects" — this is the one that gets finished. The structured-output-from-AI pattern is also a deliberate investment: understanding how to get reliable JSON from an LLM and validate it safely in a backend is something worth carrying into future projects.

## What "Working" Looks Like
Paste a few paragraphs of a course, pick 10 questions, click Generate. After a few seconds, the first question card appears — and it's obviously from the submitted text. Answer, see right/wrong immediately, move to the next card with no friction. Reach the results screen, see a clean score. Nothing crashed. You'd use this the night before an exam.

## The POC Boundary
- **Home screen:** textarea for notes, question-count selector (5 / 10 / 15), Generate button
- **Quiz screen:** one question card at a time, multiple-choice answers, immediate feedback per answer
- **Results screen:** final score, option to start over
- **Backend API route:** receives notes + question count, calls AI, returns validated JSON quiz
- **Error handling:** graceful messages for too-short input, AI failure, or timeout
- **Fallback quiz:** a pre-generated JSON quiz baked in for demo reliability
- **Visual polish:** clean, professional UI — good enough to demo on screen without embarrassment

## Later
- PDF / file upload (blocked on core flow working first; long docs can hit model token limits)
- Saving or sharing quiz results
- History of past quizzes
- User accounts

## Explicitly Cut
- **File upload (now):** adds complexity and risks hitting token limits before the core flow is proven. Pasted text is sufficient for the proof of concept and the demo.
- **Infinite / custom question count:** adds UI complexity and unpredictable API costs. A fixed selector (5 / 10 / 15) is enough.
- **Animations or transitions between cards:** nice to have, not needed to prove the loop works.
- **Deployment:** optional per hackathon rules; a localhost demo recording is sufficient to ship.
