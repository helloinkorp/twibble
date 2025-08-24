# Twibble Product Requirements Document (PRD)  

***

## 1. Product Summary

Twibble is a vocabulary learning web app with distinct, optimized flows for teachers and students.  
- **Teachers** can create, schedule, and share interactive lessons.  
- **Students** join these lessons instantly via link/QR (no signup required), progressing through daily vocabulary, phonics, and spelling activities.  
- The product prioritizes instant access, progress tracking, and offline usability for all users.

***

## 2. Core Product Requirements

- Two defined user roles: **Teacher** (creator/manager) and **Student** (learner)
- Teachers build custom lessons via manual entry, file upload, or OCR import
- Lessons are modular, grouped by days, and support scheduling/review logic
- Instant, secure sharing and joining through unique links and QR codes (no login required for students)
- All progress, lesson data, and media assets are persistently cached for speed and offline support
- Full support for interactive, accessible learning activities on mobile/tablet/desktop devices

***

## 3. Major Features

### **For Teachers**
- Dashboard to view, edit, and organize all created lessons
- Guided lesson builder with manual entry, file import (DOCX, XLSX, PDF), and OCR processes
- Flexible assignment of activity types (Vocabulary, Phonics, Spelling) to word lists
- Visual, drag-and-drop scheduler: teachers arrange lesson day breakdowns for optimal spaced learning
- Summary/review page providing lesson stats, shareable link, and QR code
- Ability to edit or delete lessons at any time

### **For Students**
- Dashboard showing all accessible lessons and daily progress
- Daily sequence of activities: Vocabulary (flip/review cards), Phonics (chunk tap/sequencing), Spelling (tile drag/spell)
- Built-in TTS (text-to-speech) and image assets to enhance each activity
- Immediate feedback and auto-save of progress throughout every activity and lesson
- Join via shared lesson link or QR code—no signup

***

## 4. User Flows

- **Teacher:**  
  Home → Teacher Dashboard → Create Lesson → Schedule Lesson → Lesson Summary/Share → Dashboard

- **Student:**  
  Home → Student Dashboard → Enter Shared Lesson → Lesson Activities (per day) → Dashboard

- **Sharing:**  
  Lesson Summary → Copy Link/Show QR → Student Link Entry → Progress/Activity Flow

***

## 5. Data & Asset Management

- **Lesson Data:** Each lesson is a structured object holding lesson metadata, words/activities, assigned schedule, and sharing info
- **Student Progress:** For every lesson, progress is saved by day/activity, resumed instantly on return (local persistent storage)
- **Assets:** 
  - Curated images/audio for common words (maps in a central asset file)
  - Automatic fallback to external APIs (Pixabay for images, ElevenLabs for TTS) for non-curated words
  - Every asset, activity state, and progress metric is cached, supporting offline/low-connectivity use
- **Phonics:**  
  - Preferred chunk-splitting from an internal reference file; fallback to basic syllable splitting when missing

***

## 6. Business & Operational Rules

- **Student experience is account-free:** Students never log in or provide personal info. Progress is local and resumes by device.
- **Teachers require minimal setup:** Only teachers need to configure optional API keys for asset fallbacks or use external services.
- **Scheduling Policy:** 
  - First day ≈ 40% of new words; rest distributed with diminishing density, last day review-only
  - Must prevent review before learning is complete; validation required on schedule submission
- **Fallbacks ensure universal operability:** Every activity can run, even if an asset fetch fails; always use placeholders or browser TTS as needed
- **Accessibility and mobile-responsiveness are enforced as product requirements, not just guidelines**

***

## 7. Limitations, Maintenance, and Upgrade Notes

- The curated asset library (for both images and phonics) must be actively maintained as content grows; fallback logic applies for all missing entries.
- All caching and persistence is local-first; future upgrades may support syncing, analytics, or group/classroom modes.
- Lesson and activity types may expand in the future, but initial launch includes only Vocabulary, Phonics, and Spelling.

***

## 8. Non-Product (now external)

> **Design system, UI component, visual, CSS, and file/build structure rules are contained in external documentation (`DESIGN_SYSTEM.md`, `BUILD_GUIDELINES.md` `FUNCTIONAL_SPEC.md`).**  
> Refer to those for all implementation, style, or technical integration norms.

***

**End of Twibble PRD**