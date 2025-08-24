# Design System: Visual Specifications

*Complete visual design guide for consistent, accessible, and beautiful user interfaces*

---

## 🎨 **Design Philosophy**

### **Core Design Principles**
1. **Clarity First**: Information hierarchy guides user attention
2. **Accessibility**: WCAG 2.1 AA compliance minimum
3. **Consistency**: Predictable patterns across all interfaces
4. **Progressive Disclosure**: Show what's needed, when it's needed
5. **Mobile-First**: Touch-friendly, thumb-reachable design
6. **Subtle Elegance**: Warm background, minimal visual barriers

### **Visual Language**
- **Warm & Welcoming**: Default #FFF7ED background creates friendly atmosphere
- **Clean & Unobtrusive**: Transparent buttons with outline-only design
- **Unified Interface**: No color separation between headers and content
- **Restrained Typography**: Minimal use of bold weights for better readability
- **Progressive Interaction**: Elements fill/highlight only on user interaction

---

## 📋 **Design System Contracts**

### **Implementation Contract**
**ALL developers and contributors agree to:**

1. **Zero Deviation Policy**: Components MUST be implemented exactly as specified
2. **Token-Only Policy**: NO hardcoded colors, spacing, or typography values
3. **Accessibility Contract**: ALL implementations MUST meet WCAG 2.1 AA standards
4. **Mobile-First Contract**: ALL components MUST work on 320px+ viewports
5. **Performance Contract**: Design implementations MUST stay within CSS budget (≤100KB)

### **Design System Compliance**
**Required before ANY UI implementation:**
- [ ] Design tokens validated against DESIGN_SYSTEM.md
- [ ] Color contrast ratios measured (4.5:1 minimum)
- [ ] Touch targets confirmed 44px+ on mobile
- [ ] Focus states implemented and tested
- [ ] Screen reader compatibility verified

### **Violation Consequences**
- **Code Review Blocking**: Non-compliant code will be rejected
- **Deployment Blocking**: UI violations prevent production deployment
- **Required Remediation**: All violations must be fixed before merge

---

## 🔄 **Change Control Process**

### **Design System Governance**
- **Design System Owner**: Lead Developer/Designer
- **Change Authority**: Requires approval from system owner
- **Documentation**: All changes must update this document
- **Backwards Compatibility**: Changes must not break existing implementations

### **Change Request Process**
1. **Issue Creation**: Document the need for change with specific use cases
2. **Impact Assessment**: Analyze effects on existing components
3. **Stakeholder Review**: Get approval from design system owner
4. **Documentation Update**: Update this file with new specifications
5. **Implementation**: Roll out changes across all components
6. **Validation**: Test all affected components for compliance

### **Emergency Changes**
For critical accessibility or performance issues:
- Immediate fixes allowed with post-hoc documentation
- Must create tracking issue within 24 hours
- Full review and documentation within 1 week

### **Deprecation Policy**
- **6-month notice**: All breaking changes require advance warning
- **Migration path**: Provide clear upgrade instructions
- **Support timeline**: Old patterns supported during transition period

---

## 🌈 **Color System**

### **Brand Colors**
```css
/* Primary Brand Colors */
--color-primary: #D97706;           /* Orange-600 - Main brand color */
--color-primary-hover: #B45309;     /* Orange-700 - Interactive states */
--color-primary-light: #FED7AA;     /* Orange-200 - Subtle backgrounds */
--color-primary-subtle: #FFF7ED;    /* Orange-50 - Light backgrounds */
--color-primary-dark: #92400E;      /* Orange-800 - Text on light backgrounds */
```

#### **When to Use Primary Colors:**
- **Primary**: Main CTAs, active states, brand elements
- **Primary Hover**: Hover states, pressed buttons
- **Primary Light**: Selected items, pill badges
- **Primary Subtle**: **DEFAULT PAGE BACKGROUND** - All pages use #FFF7ED
- **Primary Dark**: Text on light primary backgrounds

#### **Background Color System:**
- **Default Page Background**: `--color-primary-subtle` (#FFF7ED) - Used for all page backgrounds
- **Card Backgrounds**: `--color-white` (#FFFFFF) - Content containers on warm background
- **Header Background**: `--color-primary-subtle` (#FFF7ED) - **Same as page, no separation**

### **Semantic Colors**
```css
/* Success States */
--color-success: #10B981;           /* Emerald-500 */
--color-success-light: #D1FAE5;     /* Emerald-100 */
--color-success-dark: #047857;      /* Emerald-700 */

/* Warning States */
--color-warning: #F59E0B;           /* Amber-500 */
--color-warning-light: #FEF3C7;     /* Amber-100 */
--color-warning-dark: #D97706;      /* Amber-600 */

/* Error States */
--color-error: #EF4444;             /* Red-500 */
--color-error-light: #FEE2E2;       /* Red-100 */
--color-error-dark: #DC2626;        /* Red-600 */

/* Information */
--color-info: #3B82F6;              /* Blue-500 */
--color-info-light: #DBEAFE;        /* Blue-100 */
--color-info-dark: #1D4ED8;         /* Blue-700 */
```

### **Neutral Palette**
```css
/* Neutral Colors */
--color-white: #FFFFFF;
--color-gray-50: #F9FAFB;           /* Page backgrounds */
--color-gray-100: #F3F4F6;          /* Card backgrounds */
--color-gray-200: #E5E7EB;          /* Borders, dividers */
--color-gray-300: #D1D5DB;          /* Disabled states */
--color-gray-400: #9CA3AF;          /* Placeholder text */
--color-gray-500: #6B7280;          /* Secondary text */
--color-gray-600: #4B5563;          /* Primary text - light backgrounds */
--color-gray-700: #374151;          /* Headings */
--color-gray-800: #1F2937;          /* Dark headings */
--color-gray-900: #111827;          /* High contrast text */
--color-black: #000000;
```

### **Color Usage Guidelines**

#### **✅ DO:**
- Use primary colors sparingly for important actions
- Maintain 4.5:1 contrast ratio for text
- Use semantic colors consistently (green = success, red = error)
- Test colors with color blindness simulators

#### **❌ DON'T:**
- Use red and green as the only way to convey information
- Use primary colors for large background areas
- Mix warm and cool grays in the same interface
- Use pure black (#000) for text (use gray-900 instead)

---

## ✍️ **Typography System**

### **Font Family**
```css
/* Primary Font Families */
--font-family-headers: 'Poppins', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
--font-family-body: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
--font-family-mono: "SF Mono", Monaco, Inconsolata, "Roboto Mono", Consolas, monospace;
```

### **Font Usage Guidelines**
- **Large Headers (H1, H2)**: Use Poppins for distinctive, friendly feel
- **All Other Text**: Use Inter for excellent readability and interface clarity
- **Body Text, Buttons, Forms**: Inter provides clean, professional appearance
- **Code/Technical**: Monospace font for code blocks and technical content

### **Type Scale**
```css
/* Font Sizes */
--font-size-xs: 0.75rem;    /* 12px - Captions, fine print */
--font-size-sm: 0.875rem;   /* 14px - Secondary text, labels */
--font-size-base: 1rem;     /* 16px - Body text, default */
--font-size-lg: 1.125rem;   /* 18px - Large body text */
--font-size-xl: 1.25rem;    /* 20px - Small headings */
--font-size-2xl: 1.5rem;    /* 24px - Section headings */
--font-size-3xl: 1.875rem;  /* 30px - Page headings */
--font-size-4xl: 2.25rem;   /* 36px - Display headings */

/* Font Weights */
--font-weight-normal: 400;   /* Regular text */
--font-weight-medium: 500;   /* Emphasized text */
--font-weight-semibold: 600; /* Subheadings */
--font-weight-bold: 700;     /* Headings */

/* Line Heights */
--line-height-tight: 1.25;   /* Headings */
--line-height-normal: 1.5;   /* Body text */
--line-height-relaxed: 1.75; /* Reading content */
```

### **Typography Components**

#### **Headings**
```css
.heading-1 {
    font-family: var(--font-family-headers); /* Poppins for large headers */
    font-size: var(--font-size-4xl);
    font-weight: var(--font-weight-medium); /* REDUCED from bold */
    line-height: var(--line-height-tight);
    color: var(--color-gray-900);
    margin-bottom: var(--space-6);
}

.heading-2 {
    font-family: var(--font-family-headers); /* Poppins for large headers */
    font-size: var(--font-size-3xl);
    font-weight: var(--font-weight-medium); /* REDUCED from bold */
    line-height: var(--line-height-tight);
    color: var(--color-gray-800);
    margin-bottom: var(--space-4);
}

.heading-3 {
    font-family: var(--font-family-body); /* Inter for smaller headings */
    font-size: var(--font-size-2xl);
    font-weight: var(--font-weight-normal); /* REDUCED from semibold */
    line-height: var(--line-height-tight);
    color: var(--color-gray-700);
    margin-bottom: var(--space-3);
}

.heading-4 {
    font-family: var(--font-family-body); /* Inter for smaller headings */
    font-size: var(--font-size-xl);
    font-weight: var(--font-weight-normal); /* REDUCED from semibold */
    line-height: var(--line-height-normal);
    color: var(--color-gray-700);
    margin-bottom: var(--space-2);
}
```

#### **Body Text**
```css
.text-body {
    font-size: var(--font-size-base);
    font-weight: var(--font-weight-normal);
    line-height: var(--line-height-normal);
    color: var(--color-gray-600);
}

.text-body-large {
    font-size: var(--font-size-lg);
    font-weight: var(--font-weight-normal);
    line-height: var(--line-height-normal);
    color: var(--color-gray-600);
}

.text-secondary {
    font-size: var(--font-size-sm);
    font-weight: var(--font-weight-normal);
    line-height: var(--line-height-normal);
    color: var(--color-gray-500);
}

.text-caption {
    font-size: var(--font-size-xs);
    font-weight: var(--font-weight-normal);
    line-height: var(--line-height-normal);
    color: var(--color-gray-500);
    text-transform: uppercase;
    letter-spacing: 0.05em;
}
```

### **Bold Usage Guidelines - MINIMAL POLICY**
- **Default**: Use `font-weight-normal` (400) for 95% of all text
- **Buttons**: Never use bold - all button text uses normal weight
- **Navigation**: Normal weight only, no bold navigation items
- **Headings**: Maximum medium weight (500) for main page titles only
- **Content**: Use color, size, or spacing for emphasis - not bold
- **Forms**: Labels and help text use normal weight
- **Cards**: All card text uses normal weight, including titles

```css
```

---

## 🔘 **Button System**

### **Button Philosophy**
- **CTAs (Call-to-Actions)**: Filled buttons for primary actions
- **All Other Buttons**: Transparent with outline only, fill on hover
- **Typography**: Regular weight (400), no bold unless absolutely necessary
- **Progressive Enhancement**: Visual feedback only appears on interaction

### **Button Variants**

#### **CTA Buttons (Primary Actions Only)**
```css
.btn-cta {
    font-family: var(--font-family-body); /* Inter for buttons */
    background: var(--color-primary);
    color: var(--color-white);
    border: 2px solid var(--color-primary);
    border-radius: var(--radius-md);
    padding: var(--space-3) var(--space-6);
    font-size: var(--font-size-base);
    font-weight: var(--font-weight-normal); /* NO BOLD */
    cursor: pointer;
    transition: all 0.2s ease-in-out;
    
    /* Touch target minimum 44px */
    min-height: 44px;
    min-width: 44px;
}

.btn-cta:hover {
    background: var(--color-primary-hover);
    border-color: var(--color-primary-hover);
    transform: translateY(-1px);
    box-shadow: var(--shadow-md);
}

.btn-cta:focus {
    outline: none;
    box-shadow: 0 0 0 3px var(--color-primary-light);
}

.btn-cta:disabled {
    background: var(--color-gray-300);
    border-color: var(--color-gray-300);
    color: var(--color-gray-500);
    cursor: not-allowed;
    transform: none;
    box-shadow: none;
}
```

#### **Standard Buttons (Default Style)**
```css
.btn, .btn-primary {
    font-family: var(--font-family-body); /* Inter for buttons */
    background: transparent;
    color: var(--color-primary);
    border: 2px solid var(--color-primary);
    border-radius: var(--radius-md);
    padding: calc(var(--space-3) - 2px) calc(var(--space-6) - 2px);
    font-size: var(--font-size-base);
    font-weight: var(--font-weight-normal); /* NO BOLD */
    cursor: pointer;
    transition: all 0.2s ease-in-out;
    min-height: 44px;
    min-width: 44px;
}

.btn:hover, .btn-primary:hover {
    background: var(--color-primary);
    color: var(--color-white);
    border-color: var(--color-primary);
}

.btn:focus, .btn-primary:focus {
    outline: none;
    box-shadow: 0 0 0 3px var(--color-primary-light);
}

.btn:disabled, .btn-primary:disabled {
    background: transparent;
    border-color: var(--color-gray-300);
    color: var(--color-gray-400);
    cursor: not-allowed;
}
```

#### **Secondary Buttons**
```css
.btn-secondary {
    background: transparent;
    color: var(--color-gray-600);
    border: 2px solid var(--color-gray-300);
    border-radius: var(--radius-md);
    padding: calc(var(--space-3) - 2px) calc(var(--space-6) - 2px);
    font-size: var(--font-size-base);
    font-weight: var(--font-weight-normal); /* NO BOLD */
    cursor: pointer;
    transition: all 0.2s ease-in-out;
    min-height: 44px;
    min-width: 44px;
}

.btn-secondary:hover {
    background: var(--color-gray-100);
    border-color: var(--color-gray-400);
    color: var(--color-gray-800);
}
```

#### **Tertiary Buttons (Text Only)**
```css
.btn-tertiary {
    background: transparent;
    color: var(--color-primary);
    border: 2px solid transparent;
    border-radius: var(--radius-md);
    padding: calc(var(--space-3) - 2px) calc(var(--space-4) - 2px);
    font-size: var(--font-size-base);
    font-weight: var(--font-weight-normal); /* NO BOLD */
    cursor: pointer;
    transition: all 0.2s ease-in-out;
    min-height: 44px;
    min-width: 44px;
}

.btn-tertiary:hover {
    background: var(--color-primary-light);
    border-color: var(--color-primary-light);
    color: var(--color-primary-dark);
}
```

#### **Destructive Buttons**
```css
.btn-destructive {
    background: transparent;
    color: var(--color-error);
    border: 2px solid var(--color-error);
    border-radius: var(--radius-md);
    padding: calc(var(--space-3) - 2px) calc(var(--space-6) - 2px);
    font-size: var(--font-size-base);
    font-weight: var(--font-weight-normal); /* NO BOLD */
    cursor: pointer;
    transition: all 0.2s ease-in-out;
    min-height: 44px;
    min-width: 44px;
}

.btn-destructive:hover {
    background: var(--color-error);
    color: var(--color-white);
    border-color: var(--color-error);
}
```

### **Button Sizes**
```css
.btn-small {
    padding: var(--space-2) var(--space-4);
    font-size: var(--font-size-sm);
    min-height: 36px;
}

.btn-large {
    padding: var(--space-4) var(--space-8);
    font-size: var(--font-size-lg);
    min-height: 52px;
}
```

### **Icon Buttons**
```css
.btn-icon {
    width: 44px;
    height: 44px;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: var(--radius-md);
}

.btn-icon svg {
    width: 20px;
    height: 20px;
}
```

---

## 📝 **Form Elements**

### **Input Fields**
```css
.form-input {
    width: 100%;
    padding: var(--space-3) var(--space-4);
    border: 2px solid var(--color-gray-200);
    border-radius: var(--radius-md);
    font-size: var(--font-size-base);
    font-family: inherit;
    background: var(--color-white);
    transition: all 0.2s ease-in-out;
    min-height: 44px;
}

.form-input:focus {
    outline: none;
    border-color: var(--color-primary);
    box-shadow: 0 0 0 3px var(--color-primary-light);
}

.form-input:disabled {
    background: var(--color-gray-50);
    border-color: var(--color-gray-200);
    color: var(--color-gray-400);
    cursor: not-allowed;
}

.form-input.error {
    border-color: var(--color-error);
}

.form-input.error:focus {
    border-color: var(--color-error);
    box-shadow: 0 0 0 3px var(--color-error-light);
}

.form-input.success {
    border-color: var(--color-success);
}
```

### **Labels and Helper Text**
```css
.form-label {
    display: block;
    font-size: var(--font-size-sm);
    font-weight: var(--font-weight-medium);
    color: var(--color-gray-700);
    margin-bottom: var(--space-2);
}

.form-label.required::after {
    content: " *";
    color: var(--color-error);
}

.form-helper {
    font-size: var(--font-size-xs);
    color: var(--color-gray-500);
    margin-top: var(--space-1);
}

.form-error {
    font-size: var(--font-size-xs);
    color: var(--color-error);
    margin-top: var(--space-1);
    display: flex;
    align-items: center;
    gap: var(--space-1);
}
```

### **Checkboxes and Radio Buttons**
```css
.form-checkbox,
.form-radio {
    appearance: none;
    width: 20px;
    height: 20px;
    border: 2px solid var(--color-gray-300);
    background: var(--color-white);
    cursor: pointer;
    transition: all 0.2s ease-in-out;
    position: relative;
}

.form-checkbox {
    border-radius: var(--radius-sm);
}

.form-radio {
    border-radius: 50%;
}

.form-checkbox:checked,
.form-radio:checked {
    background: var(--color-primary);
    border-color: var(--color-primary);
}

.form-checkbox:checked::after {
    content: "✓";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: var(--color-white);
    font-size: var(--font-size-xs);
    font-weight: var(--font-weight-normal); /* CHECKBOX EXCEPTION - minimal visual weight */
}

.form-radio:checked::after {
    content: "";
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 8px;
    height: 8px;
    background: var(--color-white);
    border-radius: 50%;
}
```

---

## 🗂️ **Card Components**

### **Main Cards (Large Cards)**
```css
.card {
    background: var(--color-white);
    border: 1px solid var(--color-gray-200);
    border-radius: var(--radius-lg);
    padding: var(--space-6);
    box-shadow: var(--shadow-md);
    transition: all 0.2s ease-in-out;
}

.card:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
}
```

### **Small Cards (Lists, Items)**
```css
.card-small {
    background: transparent;
    border: 1px solid var(--color-gray-200);
    border-radius: var(--radius-lg);
    padding: var(--space-4);
    transition: all 0.2s ease-in-out;
    cursor: pointer;
}

.card-small:hover {
    background: var(--color-white);
    transform: translateY(-1px);
    box-shadow: var(--shadow-sm);
}
```

### **Card Variants**
```css
.card-interactive {
    cursor: pointer;
}

.card-interactive:hover {
    border-color: var(--color-primary-light);
}

.card-highlighted {
    border-color: var(--color-primary);
    background: var(--color-primary-subtle);
}

.card-compact {
    padding: var(--space-4);
}

.card-spacious {
    padding: var(--space-8);
}
```

### **Card Elements**
```css
.card-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: var(--space-4);
    padding-bottom: var(--space-3);
    border-bottom: 1px solid var(--color-gray-100);
}

.card-title {
    font-size: var(--font-size-xl);
    font-weight: var(--font-weight-semibold);
    color: var(--color-gray-900);
    margin: 0;
}

.card-subtitle {
    font-size: var(--font-size-sm);
    color: var(--color-gray-500);
    margin: 0;
    margin-top: var(--space-1);
}

.card-content {
    font-size: var(--font-size-base);
    color: var(--color-gray-600);
    line-height: var(--line-height-normal);
}

.card-footer {
    margin-top: var(--space-4);
    padding-top: var(--space-3);
    border-top: 1px solid var(--color-gray-100);
    display: flex;
    gap: var(--space-3);
    justify-content: flex-end;
}
```

---

## 🎯 **Feedback States**

### **Success States**
```css
.alert-success {
    background: var(--color-success-light);
    border: 1px solid var(--color-success);
    border-radius: var(--radius-md);
    padding: var(--space-4);
    display: flex;
    align-items: center;
    gap: var(--space-3);
}

.alert-success-icon {
    width: 20px;
    height: 20px;
    color: var(--color-success);
}

.alert-success-text {
    color: var(--color-success-dark);
    font-size: var(--font-size-sm);
    font-weight: var(--font-weight-medium);
}
```

### **Error States**
```css
.alert-error {
    background: var(--color-error-light);
    border: 1px solid var(--color-error);
    border-radius: var(--radius-md);
    padding: var(--space-4);
    display: flex;
    align-items: center;
    gap: var(--space-3);
}

.alert-error-icon {
    width: 20px;
    height: 20px;
    color: var(--color-error);
}

.alert-error-text {
    color: var(--color-error-dark);
    font-size: var(--font-size-sm);
    font-weight: var(--font-weight-medium);
}
```

### **Warning States**
```css
.alert-warning {
    background: var(--color-warning-light);
    border: 1px solid var(--color-warning);
    border-radius: var(--radius-md);
    padding: var(--space-4);
    display: flex;
    align-items: center;
    gap: var(--space-3);
}
```

### **Loading States**
```css
.loading-spinner {
    width: 24px;
    height: 24px;
    border: 3px solid var(--color-gray-200);
    border-top: 3px solid var(--color-primary);
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

.loading-skeleton {
    background: linear-gradient(90deg, var(--color-gray-100) 25%, var(--color-gray-50) 50%, var(--color-gray-100) 75%);
    background-size: 200% 100%;
    animation: shimmer 2s infinite;
    border-radius: var(--radius-sm);
}

@keyframes shimmer {
    0% { background-position: -200% 0; }
    100% { background-position: 200% 0; }
}
```

---

## 📱 **Responsive Design**

### **Breakpoint Strategy**
```css
/* Mobile First Approach */

/* Base styles (320px+) */
.container {
    padding: var(--space-4);
    max-width: 100%;
}

.grid {
    display: grid;
    gap: var(--space-4);
    grid-template-columns: 1fr;
}

/* Small tablets (640px+) */
@media (min-width: 640px) {
    .container {
        padding: var(--space-6);
    }
    
    .grid-sm-2 {
        grid-template-columns: repeat(2, 1fr);
    }
}

/* Tablets (768px+) */
@media (min-width: 768px) {
    .container {
        max-width: 768px;
        margin: 0 auto;
    }
    
    .grid-md-2 {
        grid-template-columns: repeat(2, 1fr);
    }
    
    .grid-md-3 {
        grid-template-columns: repeat(3, 1fr);
    }
}

/* Small desktop (1024px+) */
@media (min-width: 1024px) {
    .container {
        max-width: 1024px;
        padding: var(--space-8);
    }
    
    .grid-lg-4 {
        grid-template-columns: repeat(4, 1fr);
    }
}

/* Large desktop (1280px+) */
@media (min-width: 1280px) {
    .container {
        max-width: 1200px;
    }
}
```

### **Mobile-Specific Patterns**
```css
/* Touch-friendly spacing */
@media (max-width: 767px) {
    .btn {
        min-height: 48px; /* Larger touch target */
        padding: var(--space-4) var(--space-6);
    }
    
    .card {
        padding: var(--space-4); /* Less padding on mobile */
        margin-bottom: var(--space-4);
    }
    
    .form-input {
        font-size: 16px; /* Prevents zoom on iOS */
    }
}
```

---

## ♿ **Accessibility Guidelines**

### **Color Contrast Requirements**
- **Normal text**: Minimum 4.5:1 contrast ratio
- **Large text** (18px+ or 14px+ bold): Minimum 3:1 contrast ratio
- **Interactive elements**: Minimum 3:1 contrast ratio for borders/backgrounds

### **Focus Management**
```css
/* Consistent focus styles */
.focusable:focus {
    outline: none;
    box-shadow: 0 0 0 3px var(--color-primary-light);
    border-radius: var(--radius-sm);
}

/* Skip to content link */
.skip-link {
    position: absolute;
    top: -40px;
    left: 6px;
    background: var(--color-primary);
    color: var(--color-white);
    padding: var(--space-2) var(--space-4);
    border-radius: var(--radius-sm);
    text-decoration: none;
    z-index: 1000;
}

.skip-link:focus {
    top: 6px;
}
```

### **Screen Reader Support**
```html
<!-- Use semantic HTML -->
<button aria-label="Close dialog" aria-describedby="close-help">
    <svg aria-hidden="true">...</svg>
</button>
<div id="close-help" class="sr-only">
    This will close the dialog and return to the main page
</div>

<!-- Screen reader only text -->
<span class="sr-only">Loading...</span>
```

```css
.sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
}
```

---

## 🎭 **Animation & Transitions**

### **Easing Functions**
```css
:root {
    --ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
    --ease-out: cubic-bezier(0, 0, 0.2, 1);
    --ease-in: cubic-bezier(0.4, 0, 1, 1);
    --ease-bounce: cubic-bezier(0.68, -0.55, 0.265, 1.55);
}
```

### **Common Animations**
```css
/* Fade in */
@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}

/* Slide up */
@keyframes slideUp {
    from { 
        opacity: 0; 
        transform: translateY(20px); 
    }
    to { 
        opacity: 1; 
        transform: translateY(0); 
    }
}

/* Scale in */
@keyframes scaleIn {
    from { 
        opacity: 0; 
        transform: scale(0.95); 
    }
    to { 
        opacity: 1; 
        transform: scale(1); 
    }
}
```

### **Micro-interactions**
```css
/* Button hover lift */
.btn-lift:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
}

/* Card entrance animation */
.card-enter {
    animation: slideUp 0.3s var(--ease-out) forwards;
}

/* Loading pulse */
.pulse {
    animation: pulse 2s var(--ease-in-out) infinite;
}

@keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.5; }
}
```

---

## 🔍 **Icon System**

### **Icon Guidelines**
- **Size**: 16px, 20px, 24px standard sizes
- **Style**: Outline style preferred for consistency
- **Color**: Inherit parent color by default
- **Accessibility**: Always include aria-label or sr-only text

### **Common Icon Classes**
```css
.icon {
    width: 20px;
    height: 20px;
    fill: none;
    stroke: currentColor;
    stroke-width: 2;
    stroke-linecap: round;
    stroke-linejoin: round;
}

.icon-sm { width: 16px; height: 16px; }
.icon-lg { width: 24px; height: 24px; }
.icon-xl { width: 32px; height: 32px; }
```

---

## 📏 **Spacing System**

### **Spatial Hierarchy**
```css
/* Based on 4px grid system */
--space-0: 0px;      /* No space */
--space-1: 4px;      /* Tight spacing */
--space-2: 8px;      /* Close related elements */
--space-3: 12px;     /* Default small spacing */
--space-4: 16px;     /* Default medium spacing */
--space-6: 24px;     /* Section spacing */
--space-8: 32px;     /* Large section spacing */
--space-12: 48px;    /* Page section spacing */
--space-16: 64px;    /* Major section spacing */
--space-24: 96px;    /* Hero spacing */
```

### **Layout Spacing**
```css
/* Consistent margin/padding classes */
.m-0 { margin: var(--space-0); }
.m-1 { margin: var(--space-1); }
.m-2 { margin: var(--space-2); }
.m-3 { margin: var(--space-3); }
.m-4 { margin: var(--space-4); }
.m-6 { margin: var(--space-6); }
.m-8 { margin: var(--space-8); }

.p-0 { padding: var(--space-0); }
.p-1 { padding: var(--space-1); }
.p-2 { padding: var(--space-2); }
.p-3 { padding: var(--space-3); }
.p-4 { padding: var(--space-4); }
.p-6 { padding: var(--space-6); }
.p-8 { padding: var(--space-8); }

/* Direction-specific spacing */
.mt-4 { margin-top: var(--space-4); }
.mr-4 { margin-right: var(--space-4); }
.mb-4 { margin-bottom: var(--space-4); }
.ml-4 { margin-left: var(--space-4); }

.pt-4 { padding-top: var(--space-4); }
.pr-4 { padding-right: var(--space-4); }
.pb-4 { padding-bottom: var(--space-4); }
.pl-4 { padding-left: var(--space-4); }
```

---

## ✅ **Design System Checklist**

### **Before Using Components**
- [ ] Colors meet contrast requirements (4.5:1 minimum)
- [ ] Touch targets are 44px+ on mobile
- [ ] Focus states are clearly visible
- [ ] Components work without JavaScript
- [ ] Text is readable at all breakpoints

### **Component Quality Standards**
- [ ] Uses design tokens (no hardcoded values)
- [ ] Responsive across all breakpoints
- [ ] Accessible (ARIA labels, semantic HTML)
- [ ] Consistent with existing components
- [ ] Tested with screen readers
- [ ] Works in all supported browsers

### **Implementation Guidelines**
- [ ] Copy component CSS exactly as specified
- [ ] Include all states (hover, focus, disabled, error)
- [ ] Test with real content (not just Lorem ipsum)
- [ ] Validate HTML semantics
- [ ] Check color contrast with tools

---

## 🔗 **Related Documentation**

- **BUILD_GUIDELINES.md** - Technical architecture and CSS organization
- **COMPONENT_LIBRARY.md** - Interactive component examples and demos
- **ACCESSIBILITY_GUIDE.md** - Detailed accessibility implementation
- **BRAND_GUIDELINES.md** - Logo usage, voice & tone, marketing materials

---

**Remember**: This design system should evolve with your product. Update components when you discover better patterns, but always maintain consistency and accessibility standards.

---

*Last Updated: Based on modern design system practices and accessibility guidelines*