# Twibble Functional Specification

This document details functional aspects of each page and activity in Twibble, focusing on user goals, UI, interactions, and key edge cases.
*Visual/branding: See DESIGN_SYSTEM.md | Technical: See BUILD_GUIDELINES.md.*

***

## 1. Home Page (index.html)

**Goal:**  
Role selection ("Who are you today?")

**Layout/Elements:**
- Centered column layout
- App logo/title at top 
- Large "Teacher" and "Student" buttons, full-width on mobile
- Subtext: "Choose your role to get started"

**Behavior:**
- Teacher: saves role to localStorage, routes to Teacher Dashboard
- Student: saves role to localStorage, routes to Student Dashboard
- Role is persisted in localStorage for future sessions
- Role checking and redirects on all subsequent pages

***

## 2. Teacher Dashboard (teacher-dashboard.html)

**Goal:**  
Manage lessons and start new ones

**Layout/Elements:**
- Header: Home button (top right)
- Prominent "Create New Lesson" button (primary CTA styling)
- Lesson cards in responsive grid: 
  - Title, word count, creation date, activity type indicators (colored tags)
  - Actions: Edit (pencil), Share (link), Delete (trash) icons
  - Card styling: white background, border, shadow
- Scrollable lesson list with proper spacing
- Empty state: "No lessons created yet" with helpful message

**Behavior:**
- Create button → navigate to create-lesson.html
- Edit → navigate to create-lesson.html?edit=LESSON_ID with lesson pre-filled
- Share → display sharing URL with copy functionality (modal/alert for now)
- Delete → confirmation dialog → remove from localStorage → refresh display
- Real-time lesson card updates reflecting localStorage changes
- Sample lesson data included for testing and demonstration
- List always reflects addition/removal in real-time
- Role checking: redirect to home if not teacher

***

## 3. Create Lesson (create-lesson.html)

**Goal:**  
Assemble new lesson via any mix of input methods with advanced word management

**Layout/Elements:**
- Three accordion sections (expand/collapse with smooth transitions):
  1. **Manual Entry** (EXPANDED by default)
  2. **File Import** (collapsed)
  3. **OCR (Image)** (collapsed)

**Manual Entry Section:**
- 3 default word grids: "Vocabulary", "Spelling", "Phonics" (editable titles)
- Each grid has SINGLE matching toggle active by default (Vocabulary grid = Vocabulary toggle only)
- Grid components per grid:
  - Editable title input
  - Delete button (X icon, minimum 1 grid must remain)
  - 3 activity toggles: Vocabulary, Spelling, Phonics (multiple can be active)
  - Text input area: "Type words separated by commas or spaces, then press Enter"
  - Word chips display area (shows processed words)
  - "Add to Lesson" button (moves chips to final collection)
- "Add Word Group" button creates additional grids with same structure

**Word Chip System:**
- **Processing**: Enter key OR icon button processes input text into chips
- **Visual System**:
  - Single activity: Solid pastel background (blue=Vocabulary, orange=Spelling, green=Phonics)
  - Multiple activities: Gray background with small colored dots (blue/orange/green dots)
- **Full Drag & Drop**: All chips draggable between ALL grids and final lesson sections
- **Real-time Updates**: Chip colors change based on destination grid's active toggles
- **Deletion**: X button on each chip for individual removal

**File Import Section:**
- File upload: .txt/.docx/.xlsx/.pdf support
- Drag & drop zone with visual feedback
- Smart header detection for "Vocabulary", "Spelling", "Phonics" keywords
- Auto-populate word grids based on detected content structure
- Same grid management system as Manual Entry after processing
- Error handling for unsupported files or processing failures

**OCR (Image) Section:**
- "Take Photo" button (camera access) and "Upload Image" file input
- Image preview after upload
- OCR text extraction with progress feedback (Tesseract.js)
- Text correction interface allowing manual edits before processing
- Same grid management system as other sections

**Final "Lesson Words" Section:**
- Three columns: "Vocabulary Words", "Spelling Words", "Phonics Words"
- Words appear in multiple columns if assigned to multiple activities
- Same drag & drop functionality between practice type columns
- Live word count display for each activity type
- "Continue" button enabled only when words exist in final collection

**Behavior:**
- "Add to Lesson" moves ALL chips from current grid to final collection and CLEARS the grid
- Drag & drop works between all grids and final lesson sections with validation
- Toggle changes update chip colors in real-time
- Draft saving to localStorage 'currentLesson' throughout process
- Deduplication: auto-merge duplicate words with user notification
- Validation: require minimum 1 word before enabling Continue

***

## 4. Schedule Lesson (schedule-lesson.html)

**Goal:**  
Distribute words/activities over lesson days using smart algorithm with manual adjustment

**Layout/Elements:**
- Day selection: Number input (1-15) + quick-select buttons (3, 5, 7 days)
- Visual day cards showing daily word allocation
- Each card displays: day number, new word count, review word count, word previews
- Drag-and-drop interface for manual schedule adjustment
- "Reset Schedule" and "Confirm Schedule" buttons

**Behavior:**
- **Auto-Distribution Algorithm** (loads on page entry):
  - Front-loaded learning pattern: Day 1 gets ~40% of total words
  - Exponential decay for subsequent days
  - Final day: ZERO new words (review only)
  - Review pattern: Words learned on Day X appear for review on ALL subsequent days
- **Manual Adjustment**:
  - Drag words between day cards with real-time visual feedback
  - Validation rules: Cannot review words before learning them
  - Drop zone highlighting during drag operations
  - Invalid drops show error feedback
- **Controls**:
  - "Reset Schedule" regenerates automatic distribution
  - "Confirm Schedule" enabled only when schedule is valid
  - Back navigation preserves current lesson data
- **Real-time Validation**: Live error messages for invalid configurations
- **Responsive Design**: Day cards adapt to selected number of days

***

## 5. Lesson Summary/Share (lesson-summary.html)

**Goal:**  
Review complete lesson and generate sharing options

**Layout/Elements:**
- **Overview Section**:
  - Large editable lesson title field (auto-save to localStorage)
  - Statistics cards: total words, lesson duration, activity types included
  - Activity breakdown: word counts per type (Vocabulary, Spelling, Phonics)
  - Creation date and timestamp display
- **Schedule Preview Section**:
  - Collapsible day cards showing "Day X: Y new, Z review" format
  - Expandable view revealing actual word lists with activity indicators
  - "Edit Schedule" button navigating back to schedule-lesson.html
- **Sharing Section**:
  - Auto-generated unique lesson ID and shareable URL
  - Read-only URL input field with one-click copy functionality
  - QR code generation (200x200px) pointing to share URL
  - "Download QR Code" button saving QR as PNG with lesson title filename
  - "Test as Student" button opening share URL in new tab
  - Clear usage instructions for both link and QR code sharing

**Behavior:**
- **Data Finalization**: Generate permanent lesson ID, save complete lesson to localStorage 'lessons' array
- **Sharing**: URL format: student-lesson.html?id=LESSON_ID
- **Copy Functionality**: Clipboard API with success feedback ("Link copied!")
- **QR Integration**: QRious library for QR code generation and download
- **Navigation**: "Save Lesson" (primary CTA) → success confirmation → "Go to Dashboard"
- **Editing**: Title changes update localStorage in real-time
- **Testing**: "Test as Student" simulates student experience

***

## 6. Student Dashboard (student-dashboard.html)

**Goal:**  
Visualize progress across all joined lessons with day-based navigation

**Layout/Elements:**
- Header: Home button, "Student Dashboard" title
- **Lesson Cards** in responsive grid:
  - Lesson title, total word count, duration in days
  - Overall progress bar showing (completed words / total words * 100)
  - Day selector buttons in grid layout
  - "Start Learning" (new) vs "Continue" (in-progress) buttons
- Empty state: "No lessons yet. Get a lesson link from your teacher!"

**Day Button System:**
- **Three Visual States**:
  - Available: Gray styling (accessible but not current)
  - Current: Orange styling (active day with emphasis)
  - Completed: Green styling with completion indicators
- **Access Control**: Can only access current day and completed days
- **Navigation**: Day buttons route to lesson-activity.html?lesson=ID&day=X

**Behavior:**
- **Progress Calculation**: Track completion across ALL activities (vocabulary + spelling + phonics)
- **Data Loading**: Load lessons from localStorage 'studentLessons' array
- **Progress Tracking**: Detailed progress per activity type stored in localStorage
- **Role Checking**: Redirect to home if not student
- **Real-time Updates**: Progress reflects actual activity completion
- **Sample Data**: Include realistic sample lesson with partial progress for testing
- **Day Progression**: Visual indication of learning path and current position

***

## 7. Student Lesson Handler (student-lesson.html)

**Goal:**  
Process shared lesson URLs and add lessons to student collection

**Elements:**
- Loading/progress animation during lesson processing
- Success state: "Lesson added successfully!"
- Error states: Invalid ID, lesson not found, already added, corrupted data

**Behavior:**
- **URL Processing**: Extract lesson ID from URL parameter (?id=LESSON_ID)
- **Data Loading**: Load lesson data from localStorage by matching lesson ID
- **Student Integration**: Add lesson to localStorage 'studentLessons' array
- **Progress Initialization**: Create progress tracking structure for new lesson
- **Navigation**: Redirect to student dashboard after successful addition
- **Error Handling**: Clear messaging for various failure scenarios
- **Duplicate Handling**: Graceful handling if lesson already exists in student's collection

***

## 8. Lesson Activity (lesson-activity.html)

**Goal:**  
Interactive learning experience with vocabulary and spelling activities

**General Activity Flow:**
- Process by LESSON TYPE per word: Vocabulary → Spelling → Phonics (for each individual word)
- Within each lesson type: NEW words first, then REVIEW words
- Combined daily experience with seamless transitions between activity types
- Progress tracking throughout session with real-time updates

**Activity Page Structure:**
- Header: Progress indicator "Activity X of Y"
- Current word display: "Learning: 'cat'" or "Reviewing: 'dog'"
- Activity type indicator with lesson type colors
- Home and Exit buttons for navigation
- Clean, distraction-free learning environment

**8.1 Vocabulary Activity**

**NEW Words (Learning Mode):**
- Large flippable card with smooth animation
- Front side: Word image with "Tap to see the word" instruction
- Back side: Word text with auto-play TTS audio
- Control buttons: "Hear Again" and "Continue"
- Must experience both sides before advancing enabled

**REVIEW Words (Test Mode):**
- Display word image prominently at top
- Four word choice buttons in 2x2 grid layout
- Correct selection: Green flash feedback + auto-advance to next
- Incorrect selection: "Try Again!" message, same options remain
- No progression until correct answer selected

**8.2 Phonics Activity**

**Chunk Data Source:**
- Each word first attempts to use a **curated "phonics chunk" list** provided specifically for the phonics activities (e.g., ["ap", "ple"] for "apple")
- The list of curated phonics chunks for words is included in the project directory as **phonics_chunks.txt**
- When a word does **not** have curated chunk data, a **simple rule-based (syllable splitter)** fallback is used to generate chunks automatically (library suggestion: Hypher or similar, or a custom rule-based function)
- **PRD Note**: Curated chunking will be continually improved, but fallback logic ensures all words are playable in this activity

**NEW Words (Learning Mode):**
- Practice segmenting and reconstructing words:
  - Word chunks displayed as individual tap-to-hear buttons
  - Each chunk button plays TTS audio when tapped
  - Student drags chunks from scrambled position to correct sequence
  - Visual highlights during play/attempt interactions
  - Full word TTS playback available after correct assembly
  - Must complete reconstruction before advancing

**REVIEW Words (Assessment Mode):**
- Chunk sequencing/assembly challenge:
  - Scrambled chunks (from curated or fallback) presented to student
  - Student arranges chunks in correct order through drag-and-drop
  - Completion is visually confirmed with positive feedback
  - TTS plays the completed word upon successful assembly
  - "Try Again" for incorrect arrangements until success

**8.3 Spelling Activity**

**NEW Words (Learning Sequence):**
- Step 1: Show word image for 3 seconds
- Step 2: Animated letter reveal with 0.5s delays between letters
- Step 3: Auto-play TTS pronunciation after letter sequence
- Step 4: Repeat full sequence twice for reinforcement
- Step 5: Drag-and-drop practice with EXACT letters only (no distractors)

**REVIEW Words (Assessment Mode):**
- Skip learning sequence, go directly to assessment
- Clickable image that plays TTS when tapped for audio cues
- Drag-and-drop interface with correct letters PLUS distractors (~10 total letters)
- "Try Again" feedback until correct spelling achieved
- No hints or assistance - full assessment mode

**Drag-and-Drop Spelling Interface:**
- Empty letter boxes with dashed borders for target positions
- Letter tiles with solid borders and touch-friendly sizing (44px minimum)
- Real-time feedback: Green for correct placement, red flash for incorrect
- Mobile touch support with proper drag event handling
- Visual feedback during drag operations (highlight drop zones)
- Snap-to-position functionality for improved user experience

**Activity Completion & Progress:**
- Track individual word and activity completion in real-time
- Update localStorage progress data throughout session
- Handle partial session completion with resume functionality
- Navigate back to student dashboard upon day completion
- Progress synchronization with dashboard display

***

## 9. API Services & Caching

**Curated Asset Handling:**

**Images:**
- **For all curated words**: Served from Supabase (`public/word-images/[foldername]/[file name]`) as per JSON mapping file
- **All other words/images**: Pixabay API fallback, then cached in localStorage for future use
- **Error Handling**: Placeholder images when all sources fail, never block lesson progression

**Audio (TTS):**
- **For all curated words**: MP3 files served from Supabase (`public/audio/[foldername]/[file name]`) via JSON mapping
- **For non-curated words**: ElevenLabs API used with user-provided API key, 30-day localStorage caching to minimize costs
- **Fallback**: Browser speechSynthesis API when all other sources unavailable

**Phonics:**
- **Curated chunk data**: Loaded from `phonics_chunks.txt` file in project directory
- **Fallback**: Simple rule-based syllable splitting for words not present in the curated file
- **Library Suggestion**: Hypher or similar, or custom rule-based function for automatic chunking

**Caching Strategy:**
- **All API results** cached locally to reduce repeat usage and costs
- **localStorage** used for all lesson/progress/asset persistence
- **Cache Management**: Automatic cleanup and storage limit handling
- **Performance**: Check localStorage first for all media assets
- **Preloading**: Background asset loading for lesson words to ensure smooth experience

**Asset Loading Priority:**
1. Check localStorage cache first
2. Load curated assets from Supabase (images/audio with JSON mapping)
3. Fallback to API services (Pixabay for images, ElevenLabs for audio)
4. Cache all API results for future use
5. Use fallback systems (placeholders/browser TTS) when all else fails

***

## 10. Shared Patterns / Common Components

- **Navigation**: App-wide consistent headers with home/back buttons
- **Button Hierarchy**: Primary CTA (filled), Orange CTA (outline→filled), Regular Action (black outline→filled)
- **Progress Indicators**: Consistent progress bars and completion states
- **Responsive Design**: Mobile-first approach with touch-friendly interactions
- **Loading States**: Visual feedback during processing operations
- **Error Handling**: Clear messaging and graceful degradation
- **Drag & Drop**: Touch-compatible across all platforms

***

## 11. Edge Cases & Error Handling

**Media Assets:**
- Missing images: Use placeholder, never block progress
- TTS failures: Fallback to browser synthesis, continue lesson flow
- Audio loading: Show loading state, timeout after reasonable delay

**Data Integrity:**
- Corrupted lesson data: Show restore prompt, auto-reset if unrecoverable
- Missing progress data: Initialize fresh progress, preserve what's recoverable
- Invalid lesson configurations: Fallback to simplified activity, always enable progression

**Network & Storage:**
- Offline functionality: Cache essential assets, queue progress updates
- Storage limits: Intelligent cache cleanup, user notification when needed
- API failures: Graceful degradation with local fallbacks

**User Experience:**
- Interrupted sessions: Full progress recovery from localStorage
- Browser refresh: Maintain session state and resume capability
- Invalid URLs: Clear error messaging with navigation back to dashboard
- Empty lessons: Helpful messaging and alternative actions

**Activity Flow:**
- Stuck states: Always provide "Skip" or "Continue" options after timeout
- Invalid activity data: Generate simplified version, never block user progression
- Missing words for activities: Graceful handling with alternative content

***

This comprehensive functional specification covers all aspects of Twibble's user experience, interactions, and edge case handling based on our detailed discussions and agreements.