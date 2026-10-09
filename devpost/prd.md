---
doc: prd
status: approved
---

# Recall — Product Requirements

A web app for students: upload your study notes or a PDF, pick a question count, and get a multiple-choice quiz you can take immediately — with questions that come from your document.
Source: `scope.md > The Unique Kernel`, `scope.md > Who It's For`.

---

## The Core Journey

Source: `scope.md > The Core Loop`, `scope.md > What "Working" Looks Like`.

1. User opens the app and lands on the Home screen.
2. User drops a PDF or .txt file onto the upload area (or pastes text as a fallback).
3. The app reads the file and shows a file card: filename, file size, page count, a short text preview (first 2–3 lines of extracted text), and a "Ready" status.
4. User selects a question count: 5, 10, or 15.
5. User clicks "Generate quiz." The button shows a loading state while the backend calls the AI.
6. The Quiz screen appears with the first question card.
7. User picks an answer. The card locks immediately: correct answer turns green (check icon), wrong answer turns red (cross icon), correct answer is still highlighted. Other options fade. A short explanation and a source excerpt from the document appear below the options.
8. User clicks "Next" to advance. On the last question, the button reads "See results."
9. The Results screen shows the score ("7 / 10", percentage, visual indicator), a neutral one-sentence comment, and a "Review your mistakes" section listing every wrong answer with its explanation and source excerpt.
10. User chooses: "Retake quiz" (same questions, no new AI call), "New quiz from this document" (new AI call, same file), or "Start over" (back to Home).

---

## Screens and Layout

Three screens, no nested navigation:

- **Home** → **Quiz** → **Results**
- "Start over" returns to Home. "New quiz from this document" returns to Quiz (after a new AI call). "Retake quiz" replays the Quiz with the existing question set.

### Home Screen
- App name and a short tagline at the top.
- A large drag-and-drop upload area as the primary input, with an upload icon (from an icon library) and brief instructions (accepted formats, size/page limit stated clearly).
- Once a file is dropped: the upload area transforms into a file card showing a document icon, filename, file size, page count, a 2–3 line text preview, and a "Ready" status badge. A discreet "Remove" button lets the user replace the file.
- A small "Or paste your text" secondary option below the upload area (collapsible or subdued).
- A question-count selector: 5, 10, or 15 (segmented control or button group).
- A "Generate quiz" button, disabled until a valid file is ready. Shows a loading/spinner state while the AI call is in progress.

### Quiz Screen
- Progress indicator at the top: "Question 3 of 10" and a thin progress bar.
- One question card at a time, centered.
- Four multiple-choice answer options as clickable items.
- After the user picks: card locks, correct answer highlighted green with a check icon, wrong answer (if chosen) highlighted red with a cross icon, remaining options fade. Icons always accompany color so the UI does not rely on color alone.
- Below the options: a 1–2 sentence explanation of the correct answer, and a short excerpt from the uploaded document.
- A "Next" button (or "See results" on the final question) appears after an answer is selected.
- No answer can be changed after selection.
- Subtle, fast card transition — no animations that slow the user down.

### Results Screen
- Score displayed prominently: "7 / 10" with percentage and a circular or bar indicator.
- One short, neutral sentence based on the score range (no emojis).
- "Review your mistakes" section: lists each incorrectly answered question with the question text, the user's answer, the correct answer, the explanation, and the source excerpt. All data reused from the existing quiz — no additional AI call.
- If the user answered everything correctly, the mistakes section is replaced with a short confirmation message.
- Three action buttons: "Retake quiz", "New quiz from this document", "Start over".

---

## Look and Feel

Source: `scope.md > Inspiration & Identity`.

- **Overall style:** Clean, professional, sober. Looks like a developer made it with care. No generic AI-app aesthetic.
- **Typography:** One font, well-chosen, consistent across the whole app.
- **Icons:** A proper icon library (e.g. Lucide, Phosphor, or Heroicons) throughout. No emojis anywhere.
- **Color:** Restrained palette. No flashy gradients. Status colors (green for correct, red for wrong) are meaningful and used only in the quiz feedback context.
- **Accessibility:** Color is always paired with an icon for feedback states (check / cross). Sufficient contrast throughout.
- **Interactions:** Responsive to user actions but never flashy. Transitions are quick and subtle — they should not slow the user down.
- **Demo quality:** The app must look good and work reliably in a 1–3 minute screen recording.

---

## Features and Behavior

### File Upload

- The upload area accepts drag-and-drop and click-to-browse for PDF and .txt files.
- Accepted file types: `.pdf`, `.txt`.
- Clear limits stated on screen (e.g. max file size or max page count — exact values to be decided in `4-spec`).
- After a file is dropped, a brief "Reading file…" state is shown while text is extracted client-side or server-side.
- On success: the upload area becomes a file card (document icon, filename, file size, page count, 2–3 line text preview, "Ready" status).
- A "Remove" button on the card lets the user replace the file; returns to the empty upload area.
- The "Generate quiz" button remains disabled until a file has a "Ready" status.

**Error states on the file card:**
- Scanned PDF (no extractable text): "This PDF has no readable text. Try a text-based PDF."
- File too large: "This file exceeds the [X MB / X pages] limit. Try a smaller document."
- Empty file: "This file appears to be empty. Please upload a document with content."
- Wrong file type (if dropped): "Only PDF and .txt files are supported."

### Text Paste (Secondary / Fallback)

- A subdued "Or paste your text" option below the upload area.
- A textarea for pasting raw text.
- Minimum text length enforced before Generate is enabled (exact threshold to be decided in `4-spec`).
- Error if text is too short: "Please paste at least [X words / characters] of notes."

### Quiz Generation

- User selects 5, 10, or 15 questions via a selector (one must always be selected; default is 10).
- "Generate quiz" button triggers a backend API call with the extracted text and the chosen count.
- Loading state on the button while the AI call is in progress (spinner or text change).
- On success: navigate to the Quiz screen.
- On failure (AI error, timeout, or invalid JSON returned): a clear error message is shown on the Home screen with a "Try again" button. The user's uploaded file and question-count selection remain intact — no re-upload required. The "Try again" button re-triggers the same API call without any state reset.

### AI-Generated Quiz Data

Each question in the returned JSON must include:
- The question text
- Four answer options (A, B, C, D)
- The index of the correct answer
- A 1–2 sentence explanation of why the answer is correct
- A short excerpt from the source document supporting the answer

This structure is validated on the backend before being sent to the frontend. If the AI returns malformed data, the backend returns a structured error rather than passing invalid data through.

### Quiz Interaction

- Questions are shown one at a time in a card layout.
- Progress indicator: "Question N of Total" and a thin progress bar, always visible.
- Answers are selectable until one is chosen; after selection the card is locked.
- Immediate visual feedback: correct answer → green + check icon; chosen wrong answer → red + cross icon; correct answer still highlighted; other options fade.
- Explanation and source excerpt appear below the locked options.
- "Next" button appears after selection. On the final question it reads "See results."
- No way to go back to a previous question.

### Results

- Score: correct count out of total, percentage, visual indicator (circular or bar).
- Neutral one-sentence contextual message (no emojis), varying by score range.
- "Review your mistakes" section: shown only if the user got at least one wrong. Lists each wrong answer with: question text, user's answer, correct answer, explanation, source excerpt.
- If perfect score: mistakes section replaced with a short confirmation message.
- Actions: "Retake quiz" (replay same questions, no API call), "New quiz from this document" (new API call with same extracted text), "Start over" (return to Home, clear all state).

### Fallback / Demo Quiz

- A complete, pre-generated sample quiz is bundled in the codebase as a static JSON file.
- It covers a realistic topic (e.g. a short passage on a real subject) and includes all required fields: question text, four options, correct answer index, explanation, and source excerpt.
- It is used as a reliable fallback for the demo recording if the AI API is unavailable or rate-limited.
- A dev-mode trigger (e.g. an environment variable flag or a hidden UI button) loads the sample quiz instead of making a real API call, so the demo can be recorded without a live API key.
- The fallback must exercise the full UI: all question cards, feedback states, and the results screen.

---

## States and Boundaries

- **Empty / first load:** Home screen with empty upload area, selector defaulted to 10 questions, Generate button disabled.
- **File reading:** Upload area shows "Reading file…" while text extraction is in progress.
- **File ready:** File card shown with "Ready" status; Generate button enabled.
- **File error:** File card shows a specific, actionable error message; Generate button stays disabled.
- **Generating:** Generate button in loading state; user cannot submit again while the call is in progress.
- **Generation error:** Error message displayed on Home screen with a "Try again" button. File card, extracted text, and question-count selection are fully preserved — no re-upload required. "Try again" re-triggers the same API call.
- **Quiz in progress:** Cards advance forward only; no back navigation; card locks on answer selection.
- **Quiz complete:** Results screen shown; no further AI calls unless "New quiz from this document" is chosen.
- **Perfect score:** Mistakes section replaced with confirmation message.
- **No persistence:** No data is saved between sessions. Refreshing the page returns the user to the empty Home screen.

---

## Product Decisions

- **File upload is v1, not later** — it is the heart of the demo experience. Pasted text is a secondary fallback, not the primary input.
- **Text-based PDFs only** — scanned PDFs require OCR, which is out of scope. Error message explains this clearly.
- **5 / 10 / 15 fixed selector** — infinite mode was considered and cut. One API call with a known question count is predictable, cheaper, and simpler to implement and demo.
- **Explanation + source excerpt per question** — this is what proves questions come from the user's document. It's the trust signal that makes the app feel different from a generic quiz tool. It must be part of the AI's structured output.
- **Retake vs. New quiz distinction** — "Retake quiz" replays existing data (zero API calls). "New quiz from this document" triggers a new API call with the same extracted text. This must be explicit in the build to avoid accidental double calls.
- **Build order: pipeline first, upload second** — the core pipeline (text → AI → validated JSON → quiz → results) is built and verified before file upload is layered on top.
- **No color-only feedback** — correct/wrong states always pair color with an icon (check / cross) for accessibility.
- **No back navigation in quiz** — once an answer is submitted, the user moves forward only.

---

## What We're Building

- Home screen: drag-and-drop upload (PDF + .txt), file card with text preview, paste text fallback, question-count selector, Generate button with loading state, file validation errors
- Quiz screen: one card at a time, locked feedback with color + icon + explanation + source excerpt, progress indicator, Next / See results button
- Results screen: score display, neutral message, mistakes review section, three action buttons
- Backend API route: receives extracted text + question count, calls AI, validates JSON, returns structured quiz or structured error
- Fallback / demo quiz: pre-generated sample JSON bundled in the codebase, triggerable via dev flag, exercises the full UI
- Error handling: all file, generation, and validation errors surfaced with clear, actionable messages

---

## Deferred From the POC

- **PDF file upload complexity (OCR, scanned docs):** Out of scope. Only text-extractable PDFs supported.
- **Score history and past quizzes:** Requires persistence layer; not needed to prove the core loop.
- **User accounts and authentication:** No accounts in v1.
- **Sharing and export:** Adds infrastructure and UI complexity; not needed for the demo.
- **Deployment:** Optional per hackathon rules; localhost demo is sufficient.

---

## Non-Goals

- This app will NOT support scanned PDFs or OCR.
- This app will NOT save any data between sessions.
- This app will NOT have user accounts or login.
- This app will NOT make AI calls from the browser — all calls go through the backend.
- This app will NOT allow the AI response to reach the frontend without server-side validation.

---

## Open Questions

- **File size / page limit:** Exact values (MB and/or page count) to be decided in `4-spec` based on the chosen AI provider's token limits. Must be resolved before build. *(Blocks `4-spec` → build.)*
- **Minimum text length for paste fallback:** Exact character or word threshold to be decided in `4-spec`. *(Can be decided during build.)*
- **AI provider:** Not yet chosen. Tradeoffs (free tier, structured output support, latency) to be resolved in `4-spec`. *(Blocks build.)*
- **PDF text extraction library:** Client-side (e.g. pdf.js) or server-side (e.g. pdf-parse). To be decided in `4-spec`. *(Blocks build.)*
- **Fallback quiz trigger mechanism:** Dev-only environment variable flag or hidden UI button — to be decided in `4-spec`. Must trigger the full UI flow (all cards + results). *(Can be decided during build.)*
