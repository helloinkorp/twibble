# Twibble Functional Specification

*Complete functional specification for Twibble vocabulary learning web app covering all user flows, technical requirements, and system behavior*

---

## **📋 Overview**

Twibble is a vocabulary learning web app with distinct, optimized flows for teachers and students. Teachers create, schedule, and share interactive lessons while students join instantly via link/QR with no signup required, progressing through daily vocabulary, phonics, and spelling activities.

**Key Principles**:
- **Instant Access**: No signup required for students
- **Offline-First**: Full functionality after initial load
- **Mobile-Optimized**: Touch-first design for all interactions
- **Progressive Enhancement**: Works without JavaScript, enhanced with it
- **Accessibility**: WCAG 2.1 AA compliance minimum

---

## **🎯 Core Requirements**

### **User Roles**
- **Teachers**: Create, manage, schedule, and share lessons
- **Students**: Access shared lessons, complete activities, track progress

### **Technical Architecture**
- **Storage**: localStorage-first with schema validation
- **Assets**: Curated (Supabase) + API fallbacks (Pixabay, ElevenLabs)
- **Offline**: PWA with service worker caching
- **Performance**: JS ≤500KB, CSS ≤100KB, localStorage ≤8MB

### **Activity Sequence (Per Day)**
**ALWAYS**: Vocabulary → Phonics → Spelling (for each word)
- **Within each type**: NEW words first, then REVIEW words
- **Progress tracking**: Real-time persistence throughout session

---

## **🛡️ Design System Compliance Safety Net**

### **MANDATORY OVERLAY FOR ALL FUNCTIONAL SPECIFICATIONS**

**Every UI component and interaction specified in this document MUST conform to DESIGN_SYSTEM.md contracts. This section serves as a comprehensive safety net to ensure no design system violations occur during implementation.**

### **Universal Design System Requirements**

#### **🎨 Visual Compliance (ALL Components)**
- **Colors**: ONLY use CSS custom properties from DESIGN_SYSTEM.md tokens
- **Spacing**: ONLY use `--space-*` tokens, NO hardcoded pixel values
- **Typography**: ONLY use `--font-size-*` and `--font-weight-*` tokens
- **Borders**: ONLY use `--radius-*` tokens for border-radius
- **Shadows**: ONLY use `--shadow-*` tokens for box-shadow

#### **♿ Accessibility Compliance (ALL Interactive Elements)**
- **Color Contrast**: Minimum 4.5:1 ratio for all text (measured and verified)
- **Touch Targets**: Minimum 44px × 44px on mobile for ALL interactive elements
- **Focus States**: Visible focus indicators using design system focus patterns
- **Screen Readers**: Proper ARIA labels and semantic HTML structure

#### **📱 Mobile-First Compliance (ALL Layouts)**
- **Responsive Behavior**: Components must work from 320px viewport width
- **Touch Interactions**: All drag-and-drop must work with touch gestures
- **Typography**: Font sizes must scale appropriately across breakpoints

### **Component-Level Enforcement**

#### **🔍 Pre-Implementation Validation**
**Before implementing ANY component in this spec:**
1. **Reference Check**: Identify corresponding DESIGN_SYSTEM.md component
2. **Token Validation**: List all required design tokens for the component
3. **Accessibility Check**: Confirm WCAG 2.1 AA compliance plan
4. **Mobile Check**: Verify responsive behavior specifications

#### **🚫 Implementation Blocking Rules**
**BLOCK implementation if:**
- Component uses hardcoded colors, spacing, or typography
- Color contrast ratios are not measured and documented
- Touch targets are smaller than 44px on mobile
- Focus states are not implemented per design system specs
- Component deviates from DESIGN_SYSTEM.md patterns

#### **✅ Compliance Validation Checklist**
**For every component implementation:**
- [ ] All CSS values use design system tokens (no hardcoded values)
- [ ] Color contrast ratios measured and meet 4.5:1 minimum
- [ ] Touch targets confirmed ≥44px on mobile devices
- [ ] Focus states implemented using design system patterns
- [ ] Component matches DESIGN_SYSTEM.md specifications exactly
- [ ] Responsive behavior tested from 320px to 1280px
- [ ] Screen reader compatibility verified with actual testing

### **Functional Specification Override Policy**

#### **🔄 Specification Updates**
- **Design System Changes**: When DESIGN_SYSTEM.md is updated, ALL affected specifications in this document MUST be updated to maintain consistency
- **Conflict Resolution**: Design system requirements ALWAYS take precedence over functional specifications
- **Change Propagation**: Updates to design system components require review of ALL sections in this specification

#### **🎯 Implementation Priority**
1. **Design System Compliance** (HIGHEST - Non-negotiable)
2. **Accessibility Requirements** (HIGHEST - Non-negotiable) 
3. **Functional Behavior** (HIGH - Specified in sections below)
4. **Performance Requirements** (HIGH - Meet technical constraints)
5. **Enhancement Features** (MEDIUM - Optional improvements)

### **Safety Net Activation**

**This safety net is activated automatically by:**
- Code review processes (code-reviewer agent)
- Pre-deployment validation (/prepare-deploy command)
- UI conformance script (BUILD_GUIDELINES.md)
- Manual implementation reviews

**Violations will result in:**
- Implementation rejection and required remediation
- Deployment blocking until compliance achieved
- Required design system education and re-review

---

## **🏠 1. Home Page (`index.html`)**

### **Purpose**
Role selection entry point for the application.

### **UI Components**
```html
<main class="home-container">
  <header class="home-header">
    <img src="logo.png" alt="Twibble" class="logo">
    <h1 class="heading-1">Welcome to Twibble</h1>
    <p class="text-secondary">Choose your role to get started</p>
  </header>
  
  <section class="role-selection">
    <button class="btn-cta role-btn" data-role="teacher">
      <span class="role-icon">👩‍🏫</span>
      <span class="role-title">Teacher</span>
      <span class="role-subtitle">Create and manage lessons</span>
    </button>
    
    <button class="btn-cta role-btn" data-role="student">
      <span class="role-icon">👨‍🎓</span>
      <span class="role-title">Student</span>
      <span class="role-subtitle">Learn with shared lessons</span>
    </button>
  </section>
</main>
```

### **Behavior**
- **Role Selection**: Save role to `localStorage.userRole`
- **Navigation**: Route to appropriate dashboard
- **Persistence**: Remember role for future sessions
- **Responsive**: Large touch targets (44px+ minimum)

### **Data Schema**
```javascript
const UserRole = {
  role: { type: 'string', enum: ['teacher', 'student'], required: true },
  lastAccess: { type: 'string', format: 'iso-date', required: true }
};
```

---

## **👩‍🏫 2. Teacher Dashboard (`teacher-dashboard.html`)**

### **Purpose**
Central management hub for teacher's lessons and activities.

### **UI Components**
```html
<main class="dashboard-container">
  <header class="dashboard-header">
    <h1 class="heading-2">My Lessons</h1>
    <button class="btn-tertiary" onclick="goHome()">
      <span class="sr-only">Return to</span> Home
    </button>
  </header>
  
  <section class="dashboard-actions">
    <button class="btn-cta" onclick="createLesson()">
      <span aria-hidden="true">➕</span> Create New Lesson
    </button>
  </section>
  
  <section class="lessons-grid" id="lessonsContainer">
    <!-- Lesson cards populated by JavaScript -->
  </section>
  
  <div class="empty-state" id="emptyState" hidden>
    <p class="text-body">No lessons created yet</p>
    <p class="text-secondary">Create your first lesson to get started!</p>
  </div>
</main>
```

### **Lesson Card Component**
```html
<article class="card lesson-card" data-lesson-id="{{id}}">
  <header class="card-header">
    <h3 class="card-title">{{title}}</h3>
    <div class="lesson-actions">
      <button class="btn-icon" aria-label="Edit lesson" onclick="editLesson('{{id}}')">
        <span aria-hidden="true">✏️</span>
      </button>
      <button class="btn-icon" aria-label="Share lesson" onclick="shareLesson('{{id}}')">
        <span aria-hidden="true">🔗</span>
      </button>
      <button class="btn-icon btn-destructive" aria-label="Delete lesson" onclick="deleteLesson('{{id}}')">
        <span aria-hidden="true">🗑️</span>
      </button>
    </div>
  </header>
  
  <div class="card-content">
    <div class="lesson-stats">
      <span class="stat-item">
        <span class="stat-value">{{wordCount}}</span>
        <span class="stat-label">Words</span>
      </span>
      <span class="stat-item">
        <span class="stat-value">{{duration}}</span>
        <span class="stat-label">Days</span>
      </span>
    </div>
    
    <div class="activity-tags">
      <span class="tag tag-vocabulary" hidden="{{!hasVocabulary}}">Vocabulary</span>
      <span class="tag tag-phonics" hidden="{{!hasPhonics}}">Phonics</span>
      <span class="tag tag-spelling" hidden="{{!hasSpelling}}">Spelling</span>
    </div>
    
    <time class="lesson-date text-caption">Created {{createdDate}}</time>
  </div>
</article>
```

### **Behavior**
- **Create**: Navigate to `create-lesson.html`
- **Edit**: Navigate to `create-lesson.html?edit={{lessonId}}`
- **Share**: Display sharing modal with URL and QR code
- **Delete**: Confirmation dialog → remove from localStorage
- **Real-time Updates**: Reflect localStorage changes immediately

### **Data Schema**
```javascript
const TeacherLesson = {
  id: { type: 'string', required: true, minLength: 8 },
  title: { type: 'string', required: true, minLength: 1, maxLength: 100 },
  words: { type: 'object', required: true, properties: {
    vocabulary: { type: 'array', items: { type: 'string' } },
    phonics: { type: 'array', items: { type: 'string' } },
    spelling: { type: 'array', items: { type: 'string' } }
  }},
  schedule: { type: 'array', required: true, items: { type: 'object' } },
  createdAt: { type: 'string', format: 'iso-date', required: true },
  updatedAt: { type: 'string', format: 'iso-date', required: true }
};
```

---

## **📝 3. Create Lesson (`create-lesson.html`)**

### **Purpose**
Multi-modal lesson creation with manual entry, file import, and OCR capabilities.

### **Accordion Structure**
```html
<main class="create-lesson-container">
  <header class="page-header">
    <h1 class="heading-2">Create Lesson</h1>
    <button class="btn-tertiary" onclick="goBack()">← Back</button>
  </header>
  
  <div class="input-methods">
    <!-- Method 1: Manual Entry (collapsed) -->
    <section class="accordion-section expanded" data-method="manual">
      <button class="accordion-header" aria-expanded="true">
        <span class="method-icon">✍️</span>
        <span class="method-title">Manual Entry</span>
        <span class="expand-icon" aria-hidden="true">−</span>
      </button>
      
      <div class="accordion-content">
        <div class="word-grids" id="wordGrids">
          <!-- Word grids populated by JavaScript -->
        </div>
        <button class="btn-secondary" onclick="addWordGrid()">
          + Add Word Group
        </button>
      </div>
    </section>
    
    <!-- Method 2: File Import (collapsed) -->
    <section class="accordion-section" data-method="file">
      <button class="accordion-header" aria-expanded="false">
        <span class="method-icon">📄</span>
        <span class="method-title">File Import</span>
        <span class="expand-icon" aria-hidden="true">+</span>
      </button>
      
      <div class="accordion-content" hidden>
        <div class="file-upload-zone" ondrop="handleFileDrop(event)" ondragover="handleDragOver(event)">
          <input type="file" id="fileInput" accept=".txt,.docx,.xlsx,.pdf" onchange="handleFileSelect(event)">
          <label for="fileInput" class="upload-label">
            <span class="upload-icon">📁</span>
            <span>Drop files here or click to select</span>
            <span class="upload-note">Supports: TXT, DOCX, XLSX, PDF</span>
          </label>
        </div>
      </div>
    </section>
    
    <!-- Method 3: OCR (collapsed) -->
    <section class="accordion-section" data-method="ocr">
      <button class="accordion-header" aria-expanded="false">
        <span class="method-icon">📷</span>
        <span class="method-title">OCR (Image)</span>
        <span class="expand-icon" aria-hidden="true">+</span>
      </button>
      
      <div class="accordion-content" hidden>
        <div class="ocr-controls">
          <button class="btn-primary" onclick="takePhoto()">
            <span aria-hidden="true">📷</span> Take Photo
          </button>
          <input type="file" id="imageInput" accept="image/*" onchange="handleImageSelect(event)">
          <label for="imageInput" class="btn-secondary">
            <span aria-hidden="true">🖼️</span> Upload Image
          </label>
        </div>
        
        <div class="ocr-preview" id="ocrPreview" hidden>
          <img id="ocrImage" alt="Uploaded image for text extraction">
          <div class="ocr-progress" id="ocrProgress">
            <div class="loading-spinner"></div>
            <span>Extracting text...</span>
          </div>
        </div>
        
        <div class="ocr-results" id="ocrResults" hidden>
          <label for="ocrText" class="form-label">Extracted Text (editable):</label>
          <textarea id="ocrText" class="form-input" rows="6"></textarea>
          <button class="btn-primary" onclick="processOcrText()">Process Text</button>
        </div>
      </div>
    </section>
  </div>
  
  <!-- Final Lesson Collection -->
  <section class="lesson-collection">
    <h2 class="heading-3">Lesson Words</h2>
    <div class="word-columns">
      <div class="word-column" data-activity="vocabulary">
        <h3 class="column-title">Vocabulary Words</h3>
        <div class="word-list" id="vocabularyWords"></div>
        <div class="word-count">0 words</div>
      </div>
      
      <div class="word-column" data-activity="phonics">
        <h3 class="column-title">Phonics Words</h3>
        <div class="word-list" id="phonicsWords"></div>
        <div class="word-count">0 words</div>
      </div>
      
      <div class="word-column" data-activity="spelling">
        <h3 class="column-title">Spelling Words</h3>
        <div class="word-list" id="spellingWords"></div>
        <div class="word-count">0 words</div>
      </div>
    </div>
  </section>
  
  <div class="lesson-actions">
    <button class="btn-cta" id="continueBtn" onclick="proceedToScheduling()" disabled>
      Continue to Scheduling
    </button>
  </div>
</main>
```

### **Word Grid Component**
```html
<div class="word-grid" data-grid-id="{{gridId}}">
  <header class="grid-header">
    <input type="text" class="grid-title" value="{{title}}" placeholder="Word group title">
    <button class="btn-icon btn-destructive" onclick="deleteGrid('{{gridId}}')" aria-label="Delete word group">
      <span aria-hidden="true">×</span>
    </button>
  </header>
  
  <div class="activity-toggles">
    <label class="toggle-label">
      <input type="checkbox" class="activity-toggle" data-activity="vocabulary" checked="{{vocabularyActive}}">
      <span class="toggle-text">Vocabulary</span>
    </label>
    <label class="toggle-label">
      <input type="checkbox" class="activity-toggle" data-activity="phonics" checked="{{phonicsActive}}">
      <span class="toggle-text">Phonics</span>
    </label>
    <label class="toggle-label">
      <input type="checkbox" class="activity-toggle" data-activity="spelling" checked="{{spellingActive}}">
      <span class="toggle-text">Spelling</span>
    </label>
  </div>
  
  <div class="word-input-section">
    <div class="input-container">
      <input type="text" class="word-input" placeholder="Type words separated by commas or spaces, then press Enter">
      <button class="btn-icon" onclick="processWords('{{gridId}}')" aria-label="Add words">
        <span aria-hidden="true">+</span>
      </button>
    </div>
    
    <div class="word-chips" id="chips-{{gridId}}">
      <!-- Word chips populated by JavaScript -->
    </div>
  </div>
  
  <div class="grid-actions">
    <button class="btn-primary" onclick="addToLesson('{{gridId}}')">
      Add to Lesson
    </button>
  </div>
</div>
```

### **Word Chip System**
```html
<div class="word-chip" data-word="{{word}}" data-activities="{{activities}}" draggable="true">
  <span class="chip-text">{{word}}</span>
  <div class="chip-indicators">
    <span class="activity-dot vocabulary" hidden="{{!hasVocabulary}}" title="Vocabulary"></span>
    <span class="activity-dot phonics" hidden="{{!hasPhonics}}" title="Phonics"></span>
    <span class="activity-dot spelling" hidden="{{!hasSpelling}}" title="Spelling"></span>
  </div>
  <button class="chip-delete" onclick="deleteChip(this)" aria-label="Remove word">
    <span aria-hidden="true">×</span>
  </button>
</div>
```

### **Behavior**

#### **Accordion Management**
- **Default State**: Only "Manual Entry" expanded
- **Single Expansion**: Opening one section closes others
- **Keyboard Navigation**: Arrow keys navigate sections
- **Screen Reader**: Proper ARIA states and descriptions

#### **Word Processing**
- **Input Methods**: Enter key OR plus button processes text
- **Tokenization**: Split by commas, spaces, or line breaks
- **Deduplication**: Merge duplicates with notification
- **Validation**: Minimum 1 character, maximum 50 characters per word

#### **Chip Visual System**
- **Single Activity**: Solid background color (blue=Vocabulary, green=Phonics, orange=Spelling)
- **Multiple Activities**: Gray background with colored dots
- **Real-time Updates**: Colors change when dragged between grids

#### **Drag & Drop**
- **Full Functionality**: Chips draggable between ALL grids and final columns
- **Visual Feedback**: Drop zones highlight on drag over
- **Touch Support**: Long press to initiate drag on mobile
- **Accessibility**: Keyboard alternative for drag operations

### **File Import Processing**
```javascript
const FileProcessor = {
  supportedTypes: ['.txt', '.docx', '.xlsx', '.pdf'],
  
  async processFile(file) {
    const extension = file.name.toLowerCase().split('.').pop();
    switch (extension) {
      case 'txt':
        return this.processText(await file.text());
      case 'docx':
        return this.processDocx(file);
      case 'xlsx':
        return this.processExcel(file);
      case 'pdf':
        return this.processPdf(file);
      default:
        throw new Error('Unsupported file type');
    }
  },
  
  detectColumns(content) {
    // Smart detection of "Vocabulary", "Phonics", "Spelling" keywords
    // Auto-populate appropriate word grids
  }
};
```

### **OCR Integration**
```javascript
const OCRProcessor = {
  async extractText(imageFile) {
    // Tesseract.js integration
    const { data: { text } } = await Tesseract.recognize(imageFile, 'eng', {
      logger: (m) => this.updateProgress(m)
    });
    return text;
  },
  
  updateProgress(progress) {
    // Update progress indicator during OCR processing
  }
};
```

---

## **📅 4. Schedule Lesson (`schedule-lesson.html`)**

### **Purpose**
Distribute words across lesson days using smart algorithms with manual adjustment capability.

### **UI Components**
```html
<main class="schedule-container">
  <header class="page-header">
    <h1 class="heading-2">Schedule Lesson</h1>
    <button class="btn-tertiary" onclick="goBack()">← Back</button>
  </header>
  
  <section class="schedule-controls">
    <div class="duration-selector">
      <label for="durationInput" class="form-label">Lesson Duration:</label>
      <input type="number" id="durationInput" class="form-input" min="1" max="15" value="5" onchange="updateSchedule()">
      <div class="duration-presets">
        <button class="btn-secondary preset-btn" onclick="setDuration(3)">3 Days</button>
        <button class="btn-secondary preset-btn" onclick="setDuration(5)">5 Days</button>
        <button class="btn-secondary preset-btn" onclick="setDuration(7)">7 Days</button>
      </div>
    </div>
    
    <div class="schedule-actions">
      <button class="btn-secondary" onclick="resetSchedule()">Reset Schedule</button>
      <button class="btn-primary" onclick="autoDistribute()">Auto-Distribute</button>
    </div>
  </section>
  
  <section class="schedule-preview">
    <div class="day-cards" id="dayCards">
      <!-- Day cards populated by JavaScript -->
    </div>
  </section>
  
  <section class="validation-feedback" id="validationFeedback">
    <!-- Validation messages -->
  </section>
  
  <div class="schedule-actions-bottom">
    <button class="btn-cta" id="confirmScheduleBtn" onclick="confirmSchedule()" disabled>
      Confirm Schedule
    </button>
  </div>
</main>
```

### **Day Card Component**
```html
<div class="day-card" data-day="{{dayNumber}}" ondrop="handleWordDrop(event)" ondragover="allowDrop(event)">
  <header class="day-header">
    <h3 class="day-title">Day {{dayNumber}}</h3>
    <div class="day-summary">
      <span class="new-count">{{newWords}} new</span>
      <span class="review-count">{{reviewWords}} review</span>
    </div>
  </header>
  
  <div class="day-content">
    <div class="word-section new-words" data-type="new">
      <h4 class="section-title">New Words:</h4>
      <div class="word-list" id="new-day-{{dayNumber}}">
        <!-- New words for this day -->
      </div>
    </div>
    
    <div class="word-section review-words" data-type="review">
      <h4 class="section-title">Review Words:</h4>
      <div class="word-list" id="review-day-{{dayNumber}}">
        <!-- Review words for this day -->
      </div>
    </div>
  </div>
</div>
```

### **Auto-Distribution Algorithm**
```javascript
const ScheduleAlgorithm = {
  distribute(words, days) {
    const totalWords = words.length;
    const distribution = [];
    
    // Front-loaded pattern: Day 1 gets ~40% of words
    const day1Words = Math.ceil(totalWords * 0.4);
    
    // Exponential decay for remaining days
    let remaining = totalWords - day1Words;
    const decay = 0.8;
    
    distribution[0] = day1Words;
    
    for (let day = 1; day < days - 1; day++) {
      const dayWords = Math.ceil(remaining * decay);
      distribution[day] = dayWords;
      remaining -= dayWords;
    }
    
    // Final day: ZERO new words (review only)
    distribution[days - 1] = 0;
    
    // Generate review schedule
    return this.generateReviewSchedule(distribution, days);
  },
  
  generateReviewSchedule(newWords, days) {
    const schedule = [];
    
    for (let day = 0; day < days; day++) {
      schedule[day] = {
        new: newWords[day] || 0,
        review: []
      };
      
      // Add review words from previous days
      for (let prevDay = 0; prevDay < day; prevDay++) {
        if (newWords[prevDay] > 0) {
          schedule[day].review.push(prevDay);
        }
      }
    }
    
    return schedule;
  }
};
```

### **Validation Rules**
- **No Review Before Learning**: Words cannot appear in review before being introduced
- **Final Day Zero New**: Last day must be review-only
- **Minimum Words**: At least 1 word must be scheduled
- **Maximum Daily Load**: Recommend maximum 20 words per day

### **Manual Adjustment**
- **Drag & Drop**: Words draggable between days and sections
- **Real-time Validation**: Immediate feedback for invalid moves
- **Visual Feedback**: Invalid drop zones show error state
- **Undo Support**: Ability to revert changes

---

## **📋 5. Lesson Summary (`lesson-summary.html`)**

### **Purpose**
Final review of complete lesson with sharing capabilities and QR code generation.

### **UI Components**
```html
<main class="summary-container">
  <header class="page-header">
    <h1 class="heading-2">Lesson Summary</h1>
    <button class="btn-tertiary" onclick="editSchedule()">← Edit Schedule</button>
  </header>
  
  <section class="lesson-overview">
    <div class="lesson-title-section">
      <label for="lessonTitle" class="form-label">Lesson Title:</label>
      <input type="text" id="lessonTitle" class="form-input lesson-title-input" placeholder="Enter lesson title" onchange="updateTitle(event)">
    </div>
    
    <div class="lesson-stats">
      <div class="stat-card">
        <span class="stat-value">{{totalWords}}</span>
        <span class="stat-label">Total Words</span>
      </div>
      <div class="stat-card">
        <span class="stat-value">{{duration}}</span>
        <span class="stat-label">Days</span>
      </div>
      <div class="stat-card">
        <span class="stat-value">{{activityTypes.length}}</span>
        <span class="stat-label">Activity Types</span>
      </div>
    </div>
    
    <div class="activity-breakdown">
      <div class="breakdown-item">
        <span class="activity-icon vocabulary-icon" aria-hidden="true">📚</span>
        <span class="activity-name">Vocabulary</span>
        <span class="activity-count">{{vocabularyCount}} words</span>
      </div>
      <div class="breakdown-item">
        <span class="activity-icon phonics-icon" aria-hidden="true">🔤</span>
        <span class="activity-name">Phonics</span>
        <span class="activity-count">{{phonicsCount}} words</span>
      </div>
      <div class="breakdown-item">
        <span class="activity-icon spelling-icon" aria-hidden="true">✍️</span>
        <span class="activity-name">Spelling</span>
        <span class="activity-count">{{spellingCount}} words</span>
      </div>
    </div>
    
    <time class="creation-date text-caption">Created {{creationDate}}</time>
  </section>
  
  <section class="schedule-preview-section">
    <h2 class="heading-3">Lesson Schedule</h2>
    <div class="schedule-summary">
      <!-- Collapsible day cards with word lists -->
    </div>
  </section>
  
  <section class="sharing-section">
    <h2 class="heading-3">Share Lesson</h2>
    
    <div class="share-url">
      <label for="shareUrl" class="form-label">Share Link:</label>
      <div class="url-input-group">
        <input type="text" id="shareUrl" class="form-input share-url-input" readonly value="{{shareUrl}}">
        <button class="btn-primary" onclick="copyShareUrl()" aria-describedby="copyFeedback">
          <span aria-hidden="true">📋</span> Copy
        </button>
      </div>
      <div id="copyFeedback" class="copy-feedback" hidden>Link copied!</div>
    </div>
    
    <div class="qr-code-section">
      <div class="qr-code-container">
        <canvas id="qrCode" class="qr-code" width="200" height="200"></canvas>
        <p class="text-secondary">Students can scan this QR code to access the lesson</p>
      </div>
      
      <div class="qr-actions">
        <button class="btn-secondary" onclick="downloadQrCode()">
          <span aria-hidden="true">💾</span> Download QR Code
        </button>
        <button class="btn-secondary" onclick="testAsStudent()">
          <span aria-hidden="true">🎓</span> Test as Student
        </button>
      </div>
    </div>
    
    <div class="sharing-instructions">
      <h3 class="heading-4">How to Share:</h3>
      <ol class="instructions-list">
        <li>Copy the share link and send it to students</li>
        <li>Download and print the QR code for classroom display</li>
        <li>Students can access the lesson instantly - no signup required</li>
      </ol>
    </div>
  </section>
  
  <div class="summary-actions">
    <button class="btn-cta" onclick="saveLesson()">
      <span aria-hidden="true">💾</span> Save Lesson
    </button>
  </div>
</main>
```

### **Behavior**

#### **Data Finalization**
- **Unique ID Generation**: Cryptographically secure lesson ID
- **URL Generation**: Format: `student-lesson.html?id={{lessonId}}`
- **localStorage Persistence**: Save to 'lessons' array
- **Metadata**: Creation date, update timestamps, version info

#### **QR Code Generation**
```javascript
const QRManager = {
  generate(url, size = 200) {
    const qr = new QRious({
      element: document.getElementById('qrCode'),
      value: url,
      size: size,
      level: 'M'
    });
    return qr;
  },
  
  download(filename) {
    const canvas = document.getElementById('qrCode');
    const link = document.createElement('a');
    link.download = filename || 'lesson-qr-code.png';
    link.href = canvas.toDataURL();
    link.click();
  }
};
```

#### **Copy Functionality**
```javascript
const ShareManager = {
  async copyUrl(url) {
    try {
      await navigator.clipboard.writeText(url);
      this.showFeedback('Link copied!');
    } catch (err) {
      // Fallback for older browsers
      this.fallbackCopy(url);
    }
  },
  
  showFeedback(message) {
    const feedback = document.getElementById('copyFeedback');
    feedback.textContent = message;
    feedback.hidden = false;
    setTimeout(() => feedback.hidden = true, 2000);
  }
};
```

#### **Testing Integration**
- **Test as Student**: Opens share URL in new tab
- **Simulation Mode**: Special parameter to indicate test mode
- **Return Navigation**: Easy way back to teacher view

---

## **👨‍🎓 6. Student Dashboard (`student-dashboard.html`)**

### **Purpose**
Central hub for students to view all accessible lessons and track learning progress.

### **UI Components**
```html
<main class="student-dashboard-container">
  <header class="dashboard-header">
    <h1 class="heading-2">My Learning</h1>
    <button class="btn-tertiary" onclick="goHome()">Home</button>
  </header>
  
  <section class="lessons-overview" id="lessonsContainer">
    <!-- Lesson cards populated by JavaScript -->
  </section>
  
  <div class="empty-state" id="emptyState" hidden>
    <div class="empty-icon" aria-hidden="true">📚</div>
    <h2 class="empty-title">No lessons yet</h2>
    <p class="empty-message">Get a lesson link or QR code from your teacher to start learning!</p>
  </div>
</main>
```

### **Student Lesson Card**
```html
<article class="card lesson-card student-lesson" data-lesson-id="{{lessonId}}">
  <header class="card-header">
    <h3 class="card-title">{{lessonTitle}}</h3>
    <div class="lesson-meta">
      <span class="lesson-duration">{{duration}} days</span>
      <span class="total-words">{{totalWords}} words</span>
    </div>
  </header>
  
  <div class="card-content">
    <div class="progress-overview">
      <div class="progress-bar">
        <div class="progress-fill" style="width: {{progressPercentage}}%"></div>
      </div>
      <span class="progress-text">{{completedWords}} of {{totalWords}} words mastered</span>
    </div>
    
    <div class="day-selector">
      <div class="day-buttons">
        <button class="day-btn {{dayState}}" 
                data-day="{{dayNumber}}" 
                onclick="startDay('{{lessonId}}', {{dayNumber}})"
                disabled="{{!isAccessible}}"
                aria-label="Day {{dayNumber}} - {{dayStatus}}">
          <span class="day-number">{{dayNumber}}</span>
          <span class="day-status" aria-hidden="true">{{statusIcon}}</span>
        </button>
        <!-- Repeat for all days -->
      </div>
    </div>
    
    <div class="lesson-stats">
      <div class="stat-group">
        <span class="stat-item">
          <span class="activity-icon vocab-icon" aria-hidden="true">📚</span>
          <span class="stat-count">{{vocabProgress}}</span>
        </span>
        <span class="stat-item">
          <span class="activity-icon phonics-icon" aria-hidden="true">🔤</span>
          <span class="stat-count">{{phonicsProgress}}</span>
        </span>
        <span class="stat-item">
          <span class="activity-icon spelling-icon" aria-hidden="true">✍️</span>
          <span class="stat-count">{{spellingProgress}}</span>
        </span>
      </div>
    </div>
  </div>
  
  <div class="card-footer">
    <button class="btn-cta lesson-action-btn" onclick="{{actionFunction}}('{{lessonId}}')">
      {{actionText}}
    </button>
  </div>
</article>
```

### **Day Button States**
```css
/* Day button visual states */
.day-btn {
  position: relative;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  border: 2px solid var(--color-gray-300);
  background: var(--color-white);
  color: var(--color-gray-600);
}

/* Available day (can access but not current) */
.day-btn.available {
  border-color: var(--color-gray-400);
  color: var(--color-gray-700);
}

.day-btn.available:hover {
  border-color: var(--color-primary);
  background: var(--color-primary-light);
}

/* Current day (active learning day) */
.day-btn.current {
  border-color: var(--color-primary);
  background: var(--color-primary);
  color: var(--color-white);
  box-shadow: 0 0 0 3px var(--color-primary-light);
}

/* Completed day */
.day-btn.completed {
  border-color: var(--color-success);
  background: var(--color-success);
  color: var(--color-white);
}

.day-btn.completed::after {
  content: "✓";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 14px;
  font-weight: normal;
}

/* Locked day (not yet accessible) */
.day-btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
  border-color: var(--color-gray-200);
  color: var(--color-gray-400);
}
```

### **Progress Calculation**
```javascript
const StudentProgress = {
  calculateOverallProgress(lesson, studentData) {
    const totalActivities = this.countTotalActivities(lesson);
    const completedActivities = this.countCompletedActivities(studentData);
    return Math.round((completedActivities / totalActivities) * 100);
  },
  
  countTotalActivities(lesson) {
    let total = 0;
    lesson.schedule.forEach(day => {
      total += (day.vocabulary?.length || 0) * 3; // vocab, phonics, spelling per word
    });
    return total;
  },
  
  determineCurrentDay(studentData) {
    // Find first day with incomplete activities
    for (let day = 0; day < studentData.progress.length; day++) {
      if (!this.isDayComplete(studentData.progress[day])) {
        return day;
      }
    }
    return studentData.progress.length; // All days complete
  },
  
  getDayAccessibility(dayNumber, currentDay) {
    return {
      isAccessible: dayNumber <= currentDay,
      state: dayNumber < currentDay ? 'completed' :
             dayNumber === currentDay ? 'current' : 'locked'
    };
  }
};
```

### **Data Schema**
```javascript
const StudentLessonData = {
  lessonId: { type: 'string', required: true },
  lessonTitle: { type: 'string', required: true },
  accessedAt: { type: 'string', format: 'iso-date', required: true },
  lastActivity: { type: 'string', format: 'iso-date' },
  progress: { type: 'array', items: { type: 'object', properties: {
    day: { type: 'number', required: true },
    activities: { type: 'object', properties: {
      vocabulary: { type: 'array', items: { type: 'object' } },
      phonics: { type: 'array', items: { type: 'object' } },
      spelling: { type: 'array', items: { type: 'object' } }
    }}
  }}}
};
```

---

## **🔗 7. Student Lesson Handler (`student-lesson.html`)**

### **Purpose**
Process shared lesson URLs and integrate lessons into student's collection.

### **UI Components**
```html
<main class="lesson-handler-container">
  <div class="loading-state" id="loadingState">
    <div class="loading-spinner"></div>
    <h2 class="loading-title">Loading lesson...</h2>
    <p class="loading-message">Please wait while we prepare your lesson</p>
  </div>
  
  <div class="success-state" id="successState" hidden>
    <div class="success-icon" aria-hidden="true">✅</div>
    <h2 class="success-title">Lesson Added Successfully!</h2>
    <p class="success-message">You can now start learning with this lesson</p>
    
    <div class="lesson-preview">
      <h3 class="lesson-title" id="lessonTitlePreview"></h3>
      <div class="lesson-stats" id="lessonStatsPreview"></div>
    </div>
    
    <div class="success-actions">
      <button class="btn-cta" onclick="startLearning()">Start Learning</button>
      <button class="btn-secondary" onclick="goToDashboard()">View All Lessons</button>
    </div>
  </div>
  
  <div class="error-state" id="errorState" hidden>
    <div class="error-icon" aria-hidden="true">❌</div>
    <h2 class="error-title" id="errorTitle">Something went wrong</h2>
    <p class="error-message" id="errorMessage"></p>
    
    <div class="error-actions">
      <button class="btn-primary" onclick="retryLoading()">Try Again</button>
      <button class="btn-secondary" onclick="goToDashboard()">Go to Dashboard</button>
    </div>
  </div>
</main>
```

### **URL Processing Logic**
```javascript
const LessonHandler = {
  async processLessonUrl() {
    try {
      // Extract lesson ID from URL parameter
      const urlParams = new URLSearchParams(window.location.search);
      const lessonId = urlParams.get('id');
      
      if (!lessonId) {
        throw new Error('No lesson ID provided');
      }
      
      // Load lesson data from localStorage
      const lesson = this.loadLessonData(lessonId);
      if (!lesson) {
        throw new Error('Lesson not found');
      }
      
      // Check if lesson already exists in student collection
      if (this.lessonAlreadyExists(lessonId)) {
        this.showExistingLessonMessage();
        return;
      }
      
      // Add lesson to student collection
      await this.addToStudentCollection(lesson);
      
      // Initialize progress tracking
      this.initializeProgress(lessonId);
      
      this.showSuccess(lesson);
      
    } catch (error) {
      console.error('Lesson handler error:', error);
      this.showError(error.message);
    }
  },
  
  loadLessonData(lessonId) {
    const lessons = JSON.parse(localStorage.getItem('lessons') || '[]');
    return lessons.find(lesson => lesson.id === lessonId);
  },
  
  lessonAlreadyExists(lessonId) {
    const studentLessons = JSON.parse(localStorage.getItem('studentLessons') || '[]');
    return studentLessons.some(lesson => lesson.lessonId === lessonId);
  },
  
  async addToStudentCollection(lesson) {
    const studentLessons = JSON.parse(localStorage.getItem('studentLessons') || '[]');
    
    const studentLesson = {
      lessonId: lesson.id,
      lessonTitle: lesson.title,
      lessonData: lesson,
      accessedAt: new Date().toISOString(),
      lastActivity: null,
      progress: this.createInitialProgress(lesson)
    };
    
    studentLessons.push(studentLesson);
    localStorage.setItem('studentLessons', JSON.stringify(studentLessons));
  }
};
```

### **Error States**
```javascript
const ErrorStates = {
  'invalid-id': {
    title: 'Invalid Lesson Link',
    message: 'The lesson link appears to be corrupted or invalid. Please check with your teacher.'
  },
  'lesson-not-found': {
    title: 'Lesson Not Found',
    message: 'This lesson could not be found. It may have been deleted or is not available.'
  },
  'already-added': {
    title: 'Lesson Already Added',
    message: 'You already have access to this lesson. You can find it in your dashboard.'
  },
  'storage-full': {
    title: 'Storage Full',
    message: 'Your device storage is full. Please clear some space and try again.'
  },
  'network-error': {
    title: 'Connection Problem',
    message: 'Unable to load the lesson. Please check your internet connection and try again.'
  }
};
```

---

## **🎮 8. Lesson Activity (`lesson-activity.html`)**

### **Purpose**
Interactive learning environment for vocabulary, phonics, and spelling activities.

### **Activity Flow Architecture**
```
Daily Sequence: For each word → Vocabulary → Phonics → Spelling
Within each activity type: NEW words first, then REVIEW words
```

### **Main Activity Container**
```html
<main class="activity-container">
  <header class="activity-header">
    <div class="progress-section">
      <div class="activity-progress">
        <span class="progress-text">Activity <span id="currentActivity">1</span> of <span id="totalActivities">15</span></span>
        <div class="progress-bar">
          <div class="progress-fill" id="progressFill" style="width: 0%"></div>
        </div>
      </div>
    </div>
    
    <div class="activity-info">
      <span class="current-word" id="currentWord">Learning: "cat"</span>
      <span class="activity-type" id="activityType">Vocabulary - New Word</span>
    </div>
    
    <div class="header-actions">
      <button class="btn-icon" onclick="exitActivity()" aria-label="Exit to dashboard">
        <span aria-hidden="true">🏠</span>
      </button>
    </div>
  </header>
  
  <section class="activity-content" id="activityContent">
    <!-- Dynamic activity content loaded here -->
  </section>
  
  <div class="activity-navigation" id="activityNavigation">
    <!-- Navigation controls (when needed) -->
  </div>
</main>
```

### **8.1 Vocabulary Activities**

#### **NEW Words (Learning Mode)**
```html
<div class="vocabulary-learning-activity">
  <div class="flip-card" id="flipCard">
    <div class="flip-card-inner" onclick="flipCard()">
      <div class="flip-card-front">
        <img src="{{wordImage}}" alt="{{altText}}" class="word-image" id="wordImage">
        <p class="flip-instruction">Tap to see the word</p>
      </div>
      
      <div class="flip-card-back">
        <h2 class="word-text" id="wordText">{{word}}</h2>
        <button class="btn-secondary audio-btn" onclick="playAudio()" aria-label="Hear word pronunciation">
          <span aria-hidden="true">🔊</span> Hear Again
        </button>
      </div>
    </div>
  </div>
  
  <div class="learning-controls" id="learningControls" hidden>
    <p class="instruction-text">Great! You've seen both sides of the card.</p>
    <button class="btn-cta" onclick="nextActivity()">Continue</button>
  </div>
</div>
```

#### **REVIEW Words (Test Mode)**
```html
<div class="vocabulary-review-activity">
  <div class="review-prompt">
    <img src="{{wordImage}}" alt="Picture for vocabulary review" class="review-image" id="reviewImage">
    <p class="review-question">Which word matches this picture?</p>
  </div>
  
  <div class="answer-choices">
    <button class="choice-btn" data-word="{{option1}}" onclick="selectAnswer(this)">
      {{option1}}
    </button>
    <button class="choice-btn" data-word="{{option2}}" onclick="selectAnswer(this)">
      {{option2}}
    </button>
    <button class="choice-btn" data-word="{{option3}}" onclick="selectAnswer(this)">
      {{option3}}
    </button>
    <button class="choice-btn" data-word="{{correctAnswer}}" onclick="selectAnswer(this)">
      {{correctAnswer}}
    </button>
  </div>
  
  <div class="review-feedback" id="reviewFeedback" hidden>
    <p class="feedback-message" id="feedbackMessage"></p>
  </div>
</div>
```

### **8.2 Phonics Activities**

#### **Chunk Data Processing**
```javascript
const PhonicsManager = {
  async getWordChunks(word) {
    // Try curated phonics chunks first
    const curatedChunks = await this.loadCuratedChunks(word);
    if (curatedChunks) {
      return curatedChunks;
    }
    
    // Fallback to syllable splitter
    return this.generateFallbackChunks(word);
  },
  
  async loadCuratedChunks(word) {
    try {
      const response = await fetch('./phonics_chunks.txt');
      const content = await response.text();
      const lines = content.split('\n');
      
      for (const line of lines) {
        const [chunkWord, chunks] = line.split('|');
        if (chunkWord.toLowerCase() === word.toLowerCase()) {
          return chunks.split(',').map(chunk => chunk.trim());
        }
      }
      
      return null;
    } catch (error) {
      console.warn('Could not load curated chunks:', error);
      return null;
    }
  },
  
  generateFallbackChunks(word) {
    // Simple syllable-based chunking
    // This is a basic implementation - could use Hypher or similar library
    return this.basicSyllableSplit(word);
  }
};
```

#### **NEW Words (Learning Mode)**
```html
<div class="phonics-learning-activity">
  <div class="word-breakdown">
    <h2 class="target-word" id="targetWord">{{word}}</h2>
    <p class="phonics-instruction">Tap each sound to hear it, then drag to build the word</p>
  </div>
  
  <div class="chunk-buttons" id="chunkButtons">
    <button class="chunk-btn" data-chunk="{{chunk}}" onclick="playChunk(this)" 
            data-position="{{position}}" aria-label="Sound: {{chunk}}">
      {{chunk}}
    </button>
    <!-- Repeat for each chunk -->
  </div>
  
  <div class="word-builder">
    <div class="build-zone" id="buildZone" ondrop="dropChunk(event)" ondragover="allowDrop(event)">
      <div class="chunk-slots" id="chunkSlots">
        <!-- Slots populated based on chunk count -->
      </div>
    </div>
    
    <button class="btn-primary" id="checkPhonicsBtn" onclick="checkPhonicsAnswer()" disabled>
      Check Answer
    </button>
  </div>
  
  <div class="phonics-feedback" id="phonicsFeedback" hidden>
    <p class="feedback-message" id="phonicsFeedbackMessage"></p>
    <button class="btn-secondary" onclick="playFullWord()" aria-label="Hear complete word">
      <span aria-hidden="true">🔊</span> Hear Full Word
    </button>
    <button class="btn-cta" onclick="nextActivity()">Continue</button>
  </div>
</div>
```

#### **REVIEW Words (Assessment Mode)**
```html
<div class="phonics-review-activity">
  <div class="review-prompt">
    <h2 class="target-word" id="reviewTargetWord">{{word}}</h2>
    <p class="phonics-instruction">Drag the sounds in the correct order</p>
  </div>
  
  <div class="scrambled-chunks" id="scrambledChunks">
    <div class="chunk-tile" draggable="true" data-chunk="{{chunk}}" data-position="{{correctPosition}}"
         ondragstart="dragStart(event)" aria-label="Sound chunk: {{chunk}}">
      {{chunk}}
    </div>
    <!-- Scrambled chunks -->
  </div>
  
  <div class="answer-area">
    <div class="drop-zones" id="dropZones">
      <div class="drop-zone" data-position="{{position}}" 
           ondrop="dropChunkReview(event)" ondragover="allowDrop(event)"
           aria-label="Position {{position}}">
        <span class="position-number">{{position}}</span>
      </div>
      <!-- Drop zones for each position -->
    </div>
    
    <button class="btn-primary" id="checkReviewBtn" onclick="checkPhonicsReview()" disabled>
      Check Order
    </button>
  </div>
  
  <div class="review-result" id="reviewResult" hidden>
    <p class="result-message" id="resultMessage"></p>
    <button class="btn-secondary" onclick="playCompleteWord()">
      <span aria-hidden="true">🔊</span> Hear Word
    </button>
    <button class="btn-cta" onclick="nextActivity()">Continue</button>
  </div>
</div>
```

### **8.3 Spelling Activities**

#### **NEW Words (Learning Sequence)**
```html
<div class="spelling-learning-activity">
  <div class="learning-phase" id="learningPhase">
    <div class="word-introduction">
      <img src="{{wordImage}}" alt="{{word}}" class="intro-image" id="introImage">
      <div class="letter-reveal" id="letterReveal">
        <!-- Letters revealed with animation -->
      </div>
      <button class="btn-secondary audio-btn" onclick="playSpellingAudio()" 
              aria-label="Hear word pronunciation">
        <span aria-hidden="true">🔊</span> Hear Word
      </button>
    </div>
  </div>
  
  <div class="practice-phase" id="practicePhase" hidden>
    <div class="spelling-prompt">
      <img src="{{wordImage}}" alt="Picture clue" class="spelling-image">
      <p class="spelling-instruction">Drag the letters to spell the word</p>
    </div>
    
    <div class="letter-bank" id="letterBank">
      <div class="letter-tile" draggable="true" data-letter="{{letter}}" 
           ondragstart="dragLetter(event)" aria-label="Letter {{letter}}">
        {{letter}}
      </div>
      <!-- Exact letters needed, no distractors -->
    </div>
    
    <div class="spelling-area">
      <div class="letter-slots" id="letterSlots">
        <div class="letter-slot" data-position="{{position}}" 
             ondrop="dropLetter(event)" ondragover="allowDrop(event)"
             aria-label="Letter position {{position}}">
        </div>
        <!-- Slots for each letter -->
      </div>
      
      <button class="btn-primary" id="checkSpellingBtn" onclick="checkSpelling()" disabled>
        Check Spelling
      </button>
    </div>
    
    <div class="spelling-feedback" id="spellingFeedback" hidden>
      <p class="feedback-message" id="spellingFeedbackMessage"></p>
      <button class="btn-cta" onclick="nextActivity()">Continue</button>
    </div>
  </div>
</div>
```

#### **REVIEW Words (Assessment Mode)**
```html
<div class="spelling-review-activity">
  <div class="review-prompt">
    <img src="{{wordImage}}" alt="Spelling clue" class="review-spelling-image" 
         onclick="playReviewAudio()" title="Click to hear word">
    <p class="review-instruction">Listen and spell the word</p>
    <button class="btn-secondary audio-btn" onclick="playReviewAudio()">
      <span aria-hidden="true">🔊</span> Play Audio
    </button>
  </div>
  
  <div class="letter-bank-review" id="letterBankReview">
    <!-- Mix of correct letters and distractors (~10 total) -->
  </div>
  
  <div class="spelling-area-review">
    <div class="letter-slots-review" id="letterSlotsReview">
      <!-- Dynamic slots based on word length -->
    </div>
    
    <button class="btn-primary" id="checkReviewSpellingBtn" onclick="checkReviewSpelling()">
      Check Spelling
    </button>
  </div>
  
  <div class="review-spelling-feedback" id="reviewSpellingFeedback" hidden>
    <p class="feedback-message" id="reviewSpellingFeedbackMessage"></p>
    <button class="btn-secondary try-again-btn" onclick="resetSpellingReview()" 
            id="tryAgainBtn" hidden>
      Try Again
    </button>
    <button class="btn-cta next-btn" onclick="nextActivity()" id="nextBtn" hidden>
      Continue
    </button>
  </div>
</div>
```

### **Activity Flow Management**
```javascript
const ActivityFlow = {
  currentLesson: null,
  currentDay: 0,
  activities: [],
  currentIndex: 0,
  
  initializeDay(lessonId, day) {
    this.currentLesson = this.loadLesson(lessonId);
    this.currentDay = day;
    this.activities = this.generateActivitySequence(day);
    this.currentIndex = 0;
    this.preloadAssets();
    this.showActivity(0);
  },
  
  generateActivitySequence(day) {
    const daySchedule = this.currentLesson.schedule[day];
    const sequence = [];
    
    // For each word: Vocabulary → Phonics → Spelling
    daySchedule.words.forEach(word => {
      if (word.activities.includes('vocabulary')) {
        sequence.push({ type: 'vocabulary', word: word.text, mode: word.isNew ? 'learning' : 'review' });
      }
      if (word.activities.includes('phonics')) {
        sequence.push({ type: 'phonics', word: word.text, mode: word.isNew ? 'learning' : 'review' });
      }
      if (word.activities.includes('spelling')) {
        sequence.push({ type: 'spelling', word: word.text, mode: word.isNew ? 'learning' : 'review' });
      }
    });
    
    return sequence;
  },
  
  nextActivity() {
    // Save progress
    this.saveActivityProgress();
    
    if (this.currentIndex < this.activities.length - 1) {
      this.currentIndex++;
      this.showActivity(this.currentIndex);
    } else {
      this.completeDayActivities();
    }
  },
  
  saveActivityProgress() {
    const activity = this.activities[this.currentIndex];
    const progress = this.getStoredProgress();
    
    // Update progress data
    if (!progress[this.currentDay]) {
      progress[this.currentDay] = {};
    }
    if (!progress[this.currentDay][activity.type]) {
      progress[this.currentDay][activity.type] = [];
    }
    
    progress[this.currentDay][activity.type].push({
      word: activity.word,
      completed: true,
      timestamp: new Date().toISOString()
    });
    
    this.saveProgress(progress);
  }
};
```

### **Asset Preloading System**
```javascript
const AssetPreloader = {
  cache: new Map(),
  
  async preloadDayAssets(lessonId, day) {
    const lesson = this.loadLesson(lessonId);
    const dayWords = lesson.schedule[day].words;
    
    // Preload in priority order
    const preloadPromises = dayWords.map(word => [
      this.preloadImage(word.text),
      this.preloadAudio(word.text)
    ]).flat();
    
    try {
      await Promise.allSettled(preloadPromises);
      console.log('Asset preloading completed');
    } catch (error) {
      console.warn('Some assets failed to preload:', error);
    }
  },
  
  async preloadImage(word) {
    // Check curated assets first
    const curatedImage = await this.getCuratedImage(word);
    if (curatedImage) {
      return this.cacheAsset(word, 'image', curatedImage);
    }
    
    // Fallback to API
    const apiImage = await this.fetchPixabayImage(word);
    return this.cacheAsset(word, 'image', apiImage);
  },
  
  async getCuratedImage(word) {
    try {
      const response = await fetch('./consolidated_words.json');
      const words = await response.json();
      const wordData = words.find(w => w.text.toLowerCase() === word.toLowerCase());
      return wordData?.imageUrl || null;
    } catch {
      return null;
    }
  },
  
  cacheAsset(word, type, url) {
    const key = `${word}-${type}`;
    this.cache.set(key, url);
    return url;
  }
};
```

---

## **📊 9. Data Schemas & Storage**

### **Lesson Data Schema**
```javascript
const LessonSchema = {
  id: { type: 'string', required: true, minLength: 8 },
  title: { type: 'string', required: true, minLength: 1, maxLength: 100 },
  createdAt: { type: 'string', format: 'iso-date', required: true },
  updatedAt: { type: 'string', format: 'iso-date', required: true },
  
  words: { 
    type: 'object', 
    required: true,
    properties: {
      vocabulary: { type: 'array', items: { type: 'string' } },
      phonics: { type: 'array', items: { type: 'string' } },
      spelling: { type: 'array', items: { type: 'string' } }
    }
  },
  
  schedule: { 
    type: 'array', 
    required: true,
    items: { 
      type: 'object',
      properties: {
        day: { type: 'number', required: true, minimum: 0 },
        words: { 
          type: 'array',
          items: {
            type: 'object',
            properties: {
              text: { type: 'string', required: true },
              activities: { type: 'array', items: { type: 'string' } },
              isNew: { type: 'boolean', required: true },
              reviewDay: { type: 'number' }
            }
          }
        }
      }
    }
  },
  
  metadata: {
    type: 'object',
    properties: {
      totalWords: { type: 'number', minimum: 1 },
      duration: { type: 'number', minimum: 1, maximum: 15 },
      activityTypes: { type: 'array', items: { type: 'string' } }
    }
  }
};
```

### **Student Progress Schema**
```javascript
const StudentProgressSchema = {
  lessonId: { type: 'string', required: true },
  lessonTitle: { type: 'string', required: true },
  accessedAt: { type: 'string', format: 'iso-date', required: true },
  lastActivity: { type: 'string', format: 'iso-date' },
  
  progress: { 
    type: 'object',
    patternProperties: {
      "^day[0-9]+$": {
        type: 'object',
        properties: {
          vocabulary: { 
            type: 'array',
            items: {
              type: 'object',
              properties: {
                word: { type: 'string', required: true },
                completed: { type: 'boolean', required: true },
                attempts: { type: 'number', minimum: 0 },
                timestamp: { type: 'string', format: 'iso-date' }
              }
            }
          },
          phonics: { type: 'array', items: { type: 'object' } },
          spelling: { type: 'array', items: { type: 'object' } }
        }
      }
    }
  },
  
  statistics: {
    type: 'object',
    properties: {
      totalWordsCompleted: { type: 'number', minimum: 0 },
      averageAccuracy: { type: 'number', minimum: 0, maximum: 100 },
      timeSpent: { type: 'number', minimum: 0 },
      streakDays: { type: 'number', minimum: 0 }
    }
  }
};
```

### **Storage Management**
```javascript
const StorageManager = {
  KEYS: {
    USER_ROLE: 'twibble_user_role',
    LESSONS: 'twibble_lessons',
    STUDENT_LESSONS: 'twibble_student_lessons',
    CURRENT_LESSON: 'twibble_current_lesson',
    ASSET_CACHE: 'twibble_asset_cache',
    SETTINGS: 'twibble_settings'
  },
  
  MAX_STORAGE_SIZE: 8 * 1024 * 1024, // 8MB limit
  
  save(key, data) {
    try {
      const serialized = JSON.stringify(data);
      
      // Check size limits
      if (serialized.length > this.MAX_STORAGE_SIZE) {
        throw new Error('Data too large for storage');
      }
      
      // Create versioned wrapper
      const wrappedData = {
        version: '1.0.0',
        timestamp: new Date().toISOString(),
        data: data
      };
      
      localStorage.setItem(key, JSON.stringify(wrappedData));
      this.createBackup(key, wrappedData);
      
      return true;
    } catch (error) {
      if (error.name === 'QuotaExceededError') {
        this.handleStorageQuota();
        return this.save(key, data); // Retry once
      }
      throw error;
    }
  },
  
  load(key) {
    try {
      const rawData = localStorage.getItem(key);
      if (!rawData) return null;
      
      const parsed = JSON.parse(rawData);
      
      // Handle versioned data
      if (parsed.version) {
        return this.migrateIfNeeded(parsed);
      }
      
      // Legacy data format
      return parsed;
    } catch (error) {
      console.error(`Failed to load ${key}:`, error);
      return this.loadBackup(key);
    }
  },
  
  handleStorageQuota() {
    // Clean up old cached assets first
    this.cleanupAssetCache();
    
    // Remove old lesson drafts
    this.cleanupOldDrafts();
    
    console.warn('Storage quota exceeded, cleaned up old data');
  }
};
```

---

## **🔧 10. API Integration & Fallback System**

### **Asset Loading Priority**
1. **Check localStorage cache first**
2. **Load curated assets from consolidated_words.json** 
3. **Fallback to external APIs** (Pixabay for images, ElevenLabs for audio)
4. **Final fallback** (placeholder image, browser TTS)

### **Consolidated Words Data Structure**
```json
{
  "words": [
    {
      "text": "cat",
      "imageUrl": "https://supabase.url/public/word-images/animals/cat.jpg",
      "audioUrl": "https://supabase.url/public/audio/animals/cat.mp3"
    },
    {
      "text": "dog", 
      "imageUrl": "https://supabase.url/public/word-images/animals/dog.jpg",
      "audioUrl": "https://supabase.url/public/audio/animals/dog.mp3"
    }
  ]
}
```

### **Asset Manager Implementation**
```javascript
const AssetManager = {
  consolidatedWords: null,
  
  async init() {
    try {
      const response = await fetch('./consolidated_words.json');
      this.consolidatedWords = await response.json();
    } catch (error) {
      console.warn('Failed to load consolidated words:', error);
      this.consolidatedWords = { words: [] };
    }
  },
  
  async getWordImage(word) {
    // 1. Check cache
    const cached = this.getCachedAsset(word, 'image');
    if (cached) return cached;
    
    // 2. Check curated assets
    const curated = this.getCuratedImage(word);
    if (curated) {
      this.cacheAsset(word, 'image', curated);
      return curated;
    }
    
    // 3. Pixabay API fallback
    try {
      const apiImage = await this.fetchPixabayImage(word);
      this.cacheAsset(word, 'image', apiImage);
      return apiImage;
    } catch (error) {
      console.warn('Pixabay fetch failed:', error);
      return this.getPlaceholderImage(word);
    }
  },
  
  async getWordAudio(word) {
    // 1. Check cache
    const cached = this.getCachedAsset(word, 'audio');
    if (cached) return cached;
    
    // 2. Check curated assets
    const curated = this.getCuratedAudio(word);
    if (curated) {
      this.cacheAsset(word, 'audio', curated);
      return curated;
    }
    
    // 3. ElevenLabs API fallback
    try {
      const apiKey = this.getElevenLabsKey();
      if (apiKey) {
        const apiAudio = await this.fetchElevenLabsAudio(word, apiKey);
        this.cacheAsset(word, 'audio', apiAudio);
        return apiAudio;
      }
    } catch (error) {
      console.warn('ElevenLabs fetch failed:', error);
    }
    
    // 4. Browser TTS fallback
    return this.generateBrowserTTS(word);
  },
  
  getCuratedImage(word) {
    const wordData = this.consolidatedWords?.words?.find(
      w => w.text.toLowerCase() === word.toLowerCase()
    );
    return wordData?.imageUrl || null;
  },
  
  getCuratedAudio(word) {
    const wordData = this.consolidatedWords?.words?.find(
      w => w.text.toLowerCase() === word.toLowerCase()
    );
    return wordData?.audioUrl || null;
  },
  
  async fetchPixabayImage(word) {
    const apiKey = this.getPixabayKey();
    if (!apiKey) throw new Error('No Pixabay API key');
    
    const response = await fetch(
      `https://pixabay.com/api/?key=${apiKey}&q=${encodeURIComponent(word)}&image_type=photo&safesearch=true&min_width=400&min_height=300`
    );
    
    if (!response.ok) throw new Error('Pixabay API error');
    
    const data = await response.json();
    if (data.hits?.length === 0) throw new Error('No images found');
    
    return data.hits[0].webformatURL;
  },
  
  generateBrowserTTS(word) {
    // Create blob URL for browser speech synthesis
    return new Promise((resolve) => {
      if (!('speechSynthesis' in window)) {
        resolve(null);
        return;
      }
      
      const utterance = new SpeechSynthesisUtterance(word);
      utterance.rate = 0.8;
      utterance.pitch = 1.0;
      
      // This is a simplified implementation
      // Real implementation would record the synthesis to a blob
      speechSynthesis.speak(utterance);
      resolve('browser-tts'); // Return identifier for browser TTS
    });
  }
};
```

### **Phonics Chunks Data Structure**
```
// phonics_chunks.txt format
cat|c,at
dog|d,og
apple|ap,ple
elephant|el,e,phant
beautiful|beau,ti,ful
```

### **Phonics Chunk Manager**
```javascript
const PhonicsChunkManager = {
  chunks: new Map(),
  
  async init() {
    try {
      const response = await fetch('./phonics_chunks.txt');
      const content = await response.text();
      this.parseChunks(content);
    } catch (error) {
      console.warn('Failed to load phonics chunks:', error);
    }
  },
  
  parseChunks(content) {
    const lines = content.split('\n').filter(line => line.trim());
    
    lines.forEach(line => {
      const [word, chunkString] = line.split('|');
      if (word && chunkString) {
        const chunks = chunkString.split(',').map(chunk => chunk.trim());
        this.chunks.set(word.toLowerCase(), chunks);
      }
    });
  },
  
  getChunks(word) {
    const curated = this.chunks.get(word.toLowerCase());
    if (curated) return curated;
    
    // Fallback to basic syllable splitting
    return this.generateFallbackChunks(word);
  },
  
  generateFallbackChunks(word) {
    // Basic syllable splitting algorithm
    // This is simplified - a real implementation might use Hypher or similar
    const vowels = 'aeiouAEIOU';
    const chunks = [];
    let currentChunk = '';
    
    for (let i = 0; i < word.length; i++) {
      currentChunk += word[i];
      
      // Split on vowel-consonant boundaries (simplified)
      if (vowels.includes(word[i]) && 
          i < word.length - 1 && 
          !vowels.includes(word[i + 1])) {
        chunks.push(currentChunk);
        currentChunk = '';
      }
    }
    
    if (currentChunk) chunks.push(currentChunk);
    
    // Ensure at least one chunk
    return chunks.length > 0 ? chunks : [word];
  }
};
```

---

## **🔍 11. Error Handling & Edge Cases**

### **Media Asset Failures**
```javascript
const ErrorHandling = {
  handleImageError(word, error) {
    console.warn(`Image failed for word "${word}":`, error);
    
    // Show placeholder image
    const placeholder = this.generatePlaceholderImage(word);
    
    // User notification
    this.showAssetError('image', word);
    
    return placeholder;
  },
  
  handleAudioError(word, error) {
    console.warn(`Audio failed for word "${word}":`, error);
    
    // Try browser TTS as final fallback
    if ('speechSynthesis' in window) {
      return this.useBrowserTTS(word);
    }
    
    // Show error but don't block activity
    this.showAssetError('audio', word);
    return null;
  },
  
  generatePlaceholderImage(word) {
    // Create canvas with word text as placeholder
    const canvas = document.createElement('canvas');
    canvas.width = 400;
    canvas.height = 300;
    const ctx = canvas.getContext('2d');
    
    ctx.fillStyle = '#f0f0f0';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    
    ctx.fillStyle = '#333';
    ctx.font = '24px Arial';
    ctx.textAlign = 'center';
    ctx.fillText(word, canvas.width/2, canvas.height/2);
    
    return canvas.toDataURL();
  },
  
  showAssetError(type, word) {
    // Non-blocking user notification
    const message = `${type === 'image' ? 'Image' : 'Audio'} unavailable for "${word}"`;
    this.showToast(message, 'warning', 3000);
  }
};
```

### **Data Corruption Recovery**
```javascript
const DataRecovery = {
  validateLessonData(lesson) {
    try {
      // Schema validation
      const validation = this.validateSchema(lesson, LessonSchema);
      if (!validation.isValid) {
        throw new Error('Schema validation failed: ' + validation.errors.join(', '));
      }
      
      // Business rule validation
      this.validateBusinessRules(lesson);
      
      return lesson;
    } catch (error) {
      console.error('Lesson data validation failed:', error);
      return this.recoverLessonData(lesson);
    }
  },
  
  validateBusinessRules(lesson) {
    // Ensure at least one word
    const totalWords = Object.values(lesson.words).flat().length;
    if (totalWords === 0) {
      throw new Error('Lesson must have at least one word');
    }
    
    // Validate schedule consistency
    lesson.schedule.forEach((day, index) => {
      if (!day.words || day.words.length === 0) {
        throw new Error(`Day ${index} has no words scheduled`);
      }
    });
  },
  
  recoverLessonData(corruptedLesson) {
    // Attempt to salvage what we can
    const recovered = {
      id: corruptedLesson.id || this.generateId(),
      title: corruptedLesson.title || 'Recovered Lesson',
      createdAt: corruptedLesson.createdAt || new Date().toISOString(),
      updatedAt: new Date().toISOString(),
      words: this.recoverWords(corruptedLesson.words),
      schedule: this.recoverSchedule(corruptedLesson.schedule),
      metadata: this.recoverMetadata(corruptedLesson)
    };
    
    // Log recovery action
    console.warn('Lesson data recovered:', recovered.id);
    
    return recovered;
  }
};
```

### **Network & Storage Failures**
```javascript
const FailureHandling = {
  handleStorageQuotaExceeded() {
    // Progressive cleanup strategy
    const cleanupSteps = [
      () => this.clearOldAssetCache(),
      () => this.clearOldLessonDrafts(), 
      () => this.compressLessonData(),
      () => this.clearNonEssentialData()
    ];
    
    for (const cleanup of cleanupSteps) {
      try {
        cleanup();
        
        // Test if we have space now
        if (this.testStorageSpace()) {
          console.log('Storage space recovered');
          return true;
        }
      } catch (error) {
        console.warn('Cleanup step failed:', error);
      }
    }
    
    // Last resort - notify user
    this.showStorageFullDialog();
    return false;
  },
  
  handleNetworkError(operation, error) {
    console.warn(`Network error in ${operation}:`, error);
    
    // Check if we're offline
    if (!navigator.onLine) {
      this.handleOfflineMode(operation);
      return;
    }
    
    // Retry with exponential backoff
    this.retryWithBackoff(operation, 3);
  },
  
  async retryWithBackoff(operation, maxRetries) {
    for (let attempt = 1; attempt <= maxRetries; attempt++) {
      try {
        await this.delay(Math.pow(2, attempt) * 1000); // 2s, 4s, 8s delays
        return await operation();
      } catch (error) {
        if (attempt === maxRetries) {
          throw error;
        }
        console.warn(`Retry ${attempt} failed:`, error);
      }
    }
  }
};
```

### **Activity Flow Interruption**
```javascript
const ActivityRecovery = {
  saveActivityState() {
    const state = {
      lessonId: this.currentLessonId,
      day: this.currentDay,
      activityIndex: this.currentActivityIndex,
      timestamp: new Date().toISOString(),
      partialProgress: this.getPartialProgress()
    };
    
    localStorage.setItem('twibble_activity_state', JSON.stringify(state));
  },
  
  recoverActivityState() {
    try {
      const state = JSON.parse(localStorage.getItem('twibble_activity_state') || 'null');
      
      if (!state) return null;
      
      // Check if state is recent (within 24 hours)
      const stateAge = Date.now() - new Date(state.timestamp).getTime();
      if (stateAge > 24 * 60 * 60 * 1000) {
        this.clearActivityState();
        return null;
      }
      
      return state;
    } catch (error) {
      console.error('Failed to recover activity state:', error);
      this.clearActivityState();
      return null;
    }
  },
  
  showRecoveryDialog(state) {
    return new Promise((resolve) => {
      // Show modal asking user if they want to resume
      const modal = this.createRecoveryModal(state);
      
      modal.onResume = () => {
        resolve(true);
        this.closeModal(modal);
      };
      
      modal.onStartFresh = () => {
        this.clearActivityState();
        resolve(false);
        this.closeModal(modal);
      };
    });
  }
};
```

---

## **♿ 12. Accessibility Implementation**

### **Screen Reader Support**
```html
<!-- Semantic HTML structure -->
<main role="main" aria-labelledby="page-title">
  <h1 id="page-title">Vocabulary Learning Activity</h1>
  
  <!-- Live region for activity feedback -->
  <div aria-live="polite" aria-atomic="true" class="sr-only" id="activity-feedback">
    <!-- Dynamic feedback messages -->
  </div>
  
  <!-- Activity content with proper roles -->
  <section role="region" aria-labelledby="current-activity" class="activity-content">
    <h2 id="current-activity">Learning: "cat"</h2>
    
    <!-- Interactive elements -->
    <button aria-describedby="flip-help" onclick="flipCard()">
      <img src="cat.jpg" alt="Picture of a cat">
    </button>
    <div id="flip-help" class="sr-only">
      Click or tap to see the word for this picture
    </div>
  </section>
</main>
```

### **Keyboard Navigation**
```javascript
const KeyboardNav = {
  init() {
    document.addEventListener('keydown', this.handleKeyDown.bind(this));
    this.setupFocusTraps();
    this.manageFocusFlow();
  },
  
  handleKeyDown(event) {
    switch (event.key) {
      case 'Enter':
      case ' ':
        // Activate focused interactive element
        if (this.isFocusableElement(event.target)) {
          event.preventDefault();
          event.target.click();
        }
        break;
        
      case 'Escape':
        // Exit current context
        this.handleEscape();
        break;
        
      case 'ArrowUp':
      case 'ArrowDown':
        // Navigate between choices
        if (this.isChoiceContext()) {
          event.preventDefault();
          this.navigateChoices(event.key === 'ArrowUp' ? -1 : 1);
        }
        break;
    }
  },
  
  setupFocusTraps() {
    // Ensure focus stays within modal dialogs
    const modals = document.querySelectorAll('[role="dialog"]');
    modals.forEach(modal => this.trapFocus(modal));
  },
  
  trapFocus(container) {
    const focusableElements = container.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    );
    
    const firstElement = focusableElements[0];
    const lastElement = focusableElements[focusableElements.length - 1];
    
    container.addEventListener('keydown', (e) => {
      if (e.key === 'Tab') {
        if (e.shiftKey) {
          if (document.activeElement === firstElement) {
            e.preventDefault();
            lastElement.focus();
          }
        } else {
          if (document.activeElement === lastElement) {
            e.preventDefault();
            firstElement.focus();
          }
        }
      }
    });
  }
};
```

### **High Contrast Mode**
```css
/* High contrast mode detection and support */
@media (prefers-contrast: high) {
  :root {
    --color-primary: #0066CC;
    --color-success: #008000;
    --color-error: #CC0000;
    --color-warning: #FF8C00;
    
    --color-gray-100: #FFFFFF;
    --color-gray-900: #000000;
  }
  
  /* Enhanced borders for better definition */
  .card, .btn, .form-input {
    border-width: 2px;
  }
  
  /* Ensure sufficient contrast for interactive elements */
  .btn:focus {
    outline: 3px solid;
    outline-offset: 2px;
  }
}

/* Reduced motion preference */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
  
  .flip-card-inner {
    transition: none;
  }
  
  .loading-spinner {
    animation: none;
  }
}
```

### **Voice Control Support**
```javascript
const VoiceControl = {
  init() {
    if (!('webkitSpeechRecognition' in window) && !('SpeechRecognition' in window)) {
      console.log('Speech recognition not supported');
      return;
    }
    
    this.recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
    this.setupSpeechRecognition();
  },
  
  setupSpeechRecognition() {
    this.recognition.continuous = false;
    this.recognition.interimResults = false;
    this.recognition.lang = 'en-US';
    
    this.recognition.onresult = (event) => {
      const command = event.results[0][0].transcript.toLowerCase();
      this.processVoiceCommand(command);
    };
    
    this.recognition.onerror = (event) => {
      console.warn('Speech recognition error:', event.error);
    };
  },
  
  processVoiceCommand(command) {
    // Map voice commands to actions
    const commands = {
      'next': () => this.nextActivity(),
      'continue': () => this.nextActivity(),
      'repeat': () => this.repeatAudio(),
      'flip': () => this.flipCard(),
      'help': () => this.showHelp()
    };
    
    // Find matching command
    for (const [trigger, action] of Object.entries(commands)) {
      if (command.includes(trigger)) {
        action();
        break;
      }
    }
  }
};
```

---

## **📱 13. Mobile Optimization**

### **Touch Interaction Patterns**
```javascript
const TouchHandler = {
  init() {
    this.setupTouchEvents();
    this.configureViewport();
    this.preventZoom();
  },
  
  setupTouchEvents() {
    // Long press for drag initiation
    let pressTimer;
    
    document.addEventListener('touchstart', (e) => {
      const target = e.target.closest('[draggable="true"]');
      if (target) {
        pressTimer = setTimeout(() => {
          this.startDrag(target, e.touches[0]);
        }, 500); // 500ms long press
      }
    });
    
    document.addEventListener('touchend', () => {
      if (pressTimer) {
        clearTimeout(pressTimer);
        pressTimer = null;
      }
    });
    
    // Prevent double-tap zoom on buttons
    let lastTouchTime = 0;
    document.addEventListener('touchend', (e) => {
      const now = new Date().getTime();
      if (now - lastTouchTime <= 300) {
        e.preventDefault();
      }
      lastTouchTime = now;
    });
  },
  
  configureViewport() {
    // Ensure proper viewport configuration
    const viewport = document.querySelector('meta[name="viewport"]');
    if (!viewport) {
      const meta = document.createElement('meta');
      meta.name = 'viewport';
      meta.content = 'width=device-width, initial-scale=1.0, user-scalable=no';
      document.head.appendChild(meta);
    }
  },
  
  preventZoom() {
    // Prevent zoom on form inputs (iOS Safari)
    const inputs = document.querySelectorAll('input, textarea');
    inputs.forEach(input => {
      if (parseFloat(getComputedStyle(input).fontSize) < 16) {
        input.style.fontSize = '16px';
      }
    });
  }
};
```

### **Responsive Activity Layouts**
```css
/* Mobile-first activity layouts */
.activity-container {
  padding: var(--space-4);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.activity-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: var(--space-4) 0;
}

/* Flip card mobile optimization */
.flip-card {
  width: min(90vw, 400px);
  height: min(60vh, 300px);
  cursor: pointer;
}

.flip-card-inner {
  transition: transform 0.3s ease-in-out;
}

/* Touch-friendly buttons */
.choice-btn {
  min-height: 56px;
  padding: var(--space-4) var(--space-6);
  margin-bottom: var(--space-3);
  font-size: var(--font-size-lg);
  width: 100%;
}

@media (min-width: 640px) {
  .choice-btn {
    width: calc(50% - var(--space-2));
    margin-right: var(--space-4);
  }
  
  .choice-btn:nth-child(even) {
    margin-right: 0;
  }
}

/* Drag and drop mobile adaptations */
.letter-tile {
  min-width: 48px;
  min-height: 48px;
  font-size: var(--font-size-xl);
  margin: var(--space-2);
  border-radius: var(--radius-md);
  background: var(--color-white);
  border: 2px solid var(--color-gray-300);
  display: flex;
  align-items: center;
  justify-content: center;
  user-select: none;
  -webkit-user-select: none;
}

.letter-tile.dragging {
  opacity: 0.5;
  transform: scale(1.1);
  z-index: 1000;
}

.letter-slot {
  min-width: 56px;
  min-height: 56px;
  border: 2px dashed var(--color-gray-300);
  border-radius: var(--radius-md);
  display: flex;
  align-items: center;
  justify-content: center;
  margin: var(--space-2);
}

.letter-slot.drop-target {
  border-color: var(--color-primary);
  background: var(--color-primary-light);
}
```

### **Performance Optimization for Mobile**
```javascript
const MobileOptimizer = {
  init() {
    this.setupLazyLoading();
    this.optimizeAnimations();
    this.enableHardwareAcceleration();
    this.manageBatteryUsage();
  },
  
  setupLazyLoading() {
    // Intersection Observer for images
    const imageObserver = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          const img = entry.target;
          img.src = img.dataset.src;
          img.classList.remove('lazy');
          imageObserver.unobserve(img);
        }
      });
    });
    
    document.querySelectorAll('img[data-src]').forEach(img => {
      imageObserver.observe(img);
    });
  },
  
  optimizeAnimations() {
    // Use requestAnimationFrame for smooth animations
    const animateProperty = (element, property, from, to, duration) => {
      const start = performance.now();
      
      const animate = (currentTime) => {
        const elapsed = currentTime - start;
        const progress = Math.min(elapsed / duration, 1);
        
        const value = from + (to - from) * this.easeInOutCubic(progress);
        element.style[property] = value + (property.includes('opacity') ? '' : 'px');
        
        if (progress < 1) {
          requestAnimationFrame(animate);
        }
      };
      
      requestAnimationFrame(animate);
    };
  },
  
  enableHardwareAcceleration() {
    // Add will-change hints for frequently animated elements
    const animatedElements = document.querySelectorAll(
      '.flip-card-inner, .letter-tile, .progress-fill'
    );
    
    animatedElements.forEach(element => {
      element.style.willChange = 'transform';
    });
  },
  
  manageBatteryUsage() {
    // Reduce activity when battery is low
    if ('getBattery' in navigator) {
      navigator.getBattery().then(battery => {
        if (battery.level < 0.2) {
          this.enablePowerSaveMode();
        }
        
        battery.addEventListener('levelchange', () => {
          if (battery.level < 0.2) {
            this.enablePowerSaveMode();
          } else {
            this.disablePowerSaveMode();
          }
        });
      });
    }
  },
  
  enablePowerSaveMode() {
    // Reduce animations and effects
    document.body.classList.add('power-save-mode');
    
    // Disable non-essential animations
    document.querySelectorAll('.loading-spinner').forEach(spinner => {
      spinner.style.animationPlayState = 'paused';
    });
  }
};
```

---

## **🔄 14. Progressive Web App (PWA) Implementation**

### **Service Worker Strategy**
```javascript
// sw.js - Service Worker
const CACHE_VERSION = 'v1.0.0';
const STATIC_CACHE = 'twibble-static-' + CACHE_VERSION;
const DYNAMIC_CACHE = 'twibble-dynamic-' + CACHE_VERSION;

const STATIC_ASSETS = [
  '/',
  '/index.html',
  '/teacher-dashboard.html',
  '/student-dashboard.html',
  '/create-lesson.html',
  '/schedule-lesson.html',
  '/lesson-summary.html',
  '/student-lesson.html',
  '/lesson-activity.html',
  '/styles.css',
  '/script.js',
  '/consolidated_words.json',
  '/phonics_chunks.txt',
  '/manifest.json'
];

// Install event - cache static assets
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(STATIC_CACHE)
      .then(cache => cache.addAll(STATIC_ASSETS))
      .then(() => self.skipWaiting())
  );
});

// Activate event - clean up old caches
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys()
      .then(cacheNames => {
        return Promise.all(
          cacheNames.map(cacheName => {
            if (cacheName !== STATIC_CACHE && cacheName !== DYNAMIC_CACHE) {
              return caches.delete(cacheName);
            }
          })
        );
      })
      .then(() => self.clients.claim())
  );
});

// Fetch event - cache-first strategy for static, network-first for dynamic
self.addEventListener('fetch', (event) => {
  const { request } = event;
  const url = new URL(request.url);
  
  // Handle API requests
  if (url.pathname.includes('/api/')) {
    event.respondWith(networkFirstStrategy(request));
    return;
  }
  
  // Handle asset requests
  if (request.destination === 'image' || request.destination === 'audio') {
    event.respondWith(cacheFirstStrategy(request));
    return;
  }
  
  // Handle navigation requests
  if (request.mode === 'navigate') {
    event.respondWith(
      caches.match(request)
        .then(response => response || fetch(request))
        .catch(() => caches.match('/index.html'))
    );
    return;
  }
  
  // Default cache-first strategy
  event.respondWith(cacheFirstStrategy(request));
});

const cacheFirstStrategy = async (request) => {
  const cached = await caches.match(request);
  if (cached) return cached;
  
  try {
    const response = await fetch(request);
    const cache = await caches.open(DYNAMIC_CACHE);
    cache.put(request, response.clone());
    return response;
  } catch (error) {
    console.warn('Fetch failed:', error);
    return new Response('Offline', { status: 503 });
  }
};

const networkFirstStrategy = async (request) => {
  try {
    const response = await fetch(request);
    const cache = await caches.open(DYNAMIC_CACHE);
    cache.put(request, response.clone());
    return response;
  } catch (error) {
    const cached = await caches.match(request);
    return cached || new Response('Offline', { status: 503 });
  }
};
```

### **Manifest Configuration**
```json
{
  "name": "Twibble - Vocabulary Learning",
  "short_name": "Twibble",
  "description": "Interactive vocabulary learning for students and teachers",
  "start_url": "/",
  "display": "standalone",
  "orientation": "portrait",
  "theme_color": "#D97706",
  "background_color": "#FFF7ED",
  
  "icons": [
    {
      "src": "/icons/icon-72x72.png",
      "sizes": "72x72",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-96x96.png",
      "sizes": "96x96",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-128x128.png",
      "sizes": "128x128",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-144x144.png",
      "sizes": "144x144",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-152x152.png",
      "sizes": "152x152",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-192x192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "/icons/icon-384x384.png",
      "sizes": "384x384",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ],
  
  "categories": ["education", "productivity"],
  "screenshots": [
    {
      "src": "/screenshots/teacher-dashboard.png",
      "sizes": "1280x720",
      "type": "image/png"
    },
    {
      "src": "/screenshots/student-activity.png", 
      "sizes": "1280x720",
      "type": "image/png"
    }
  ]
}
```

### **Background Sync Implementation**
```javascript
// Background sync for progress updates
self.addEventListener('sync', (event) => {
  if (event.tag === 'progress-sync') {
    event.waitUntil(syncProgress());
  }
});

const syncProgress = async () => {
  try {
    // Get pending progress updates from IndexedDB
    const pendingUpdates = await getPendingProgressUpdates();
    
    for (const update of pendingUpdates) {
      try {
        await syncProgressUpdate(update);
        await removePendingUpdate(update.id);
      } catch (error) {
        console.warn('Failed to sync progress update:', error);
      }
    }
  } catch (error) {
    console.error('Background sync failed:', error);
  }
};

// Register background sync when progress is saved offline
const queueProgressUpdate = async (progressData) => {
  if ('serviceWorker' in navigator && 'sync' in window.ServiceWorkerRegistration.prototype) {
    try {
      await storePendingUpdate(progressData);
      const registration = await navigator.serviceWorker.ready;
      await registration.sync.register('progress-sync');
    } catch (error) {
      console.warn('Background sync registration failed:', error);
    }
  }
};
```

### **Installation Prompt**
```javascript
const PWAInstaller = {
  deferredPrompt: null,
  
  init() {
    window.addEventListener('beforeinstallprompt', (e) => {
      e.preventDefault();
      this.deferredPrompt = e;
      this.showInstallButton();
    });
    
    window.addEventListener('appinstalled', () => {
      this.hideInstallButton();
      this.trackInstall();
    });
  },
  
  showInstallButton() {
    const installBtn = document.getElementById('install-button');
    if (installBtn) {
      installBtn.hidden = false;
      installBtn.addEventListener('click', this.promptInstall.bind(this));
    }
  },
  
  async promptInstall() {
    if (!this.deferredPrompt) return;
    
    this.deferredPrompt.prompt();
    const { outcome } = await this.deferredPrompt.userChoice;
    
    if (outcome === 'accepted') {
      this.trackInstallAccepted();
    }
    
    this.deferredPrompt = null;
  },
  
  hideInstallButton() {
    const installBtn = document.getElementById('install-button');
    if (installBtn) {
      installBtn.hidden = true;
    }
  }
};
```

---

This comprehensive functional specification covers all aspects of the Twibble vocabulary learning application, from user interfaces to technical implementation details. The specification ensures accessibility, mobile optimization, offline functionality, and robust error handling while maintaining a clean, intuitive user experience for both teachers and students.