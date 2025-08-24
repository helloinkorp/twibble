# Build Guidelines - Authoritative Reference

*Definitive technical architecture and build guidelines for Twibble vocabulary learning web application*

---

## 🎯 **Core Principles**

### **1. Data Integrity First**
- Every data operation must be validated
- Always have a fallback for failed operations
- Never lose user data, even during errors

### **2. Mobile-First Architecture**
- Design for 320px first, scale up
- Touch-friendly interactions (44px+ targets)
- Progressive enhancement for larger screens

### **3. Progressive Enhancement**
- Core functionality works without JavaScript
- Enhanced features load incrementally
- Graceful degradation for unsupported browsers

### **4. Error Resilience**
- Expect everything to fail
- Provide meaningful error messages to users
- Always have a way to recover

---

## 🏗️ **Project Structure Blueprint**

```
your-app/
├── index.html              # Entry point
├── js/
│   ├── app.js             # Main application object & initialization
│   ├── data-manager.js    # Data persistence & validation
│   ├── schemas.js         # All data validation schemas
│   ├── [feature]-manager.js # Feature-specific modules
│   └── utils.js           # Shared utilities
├── css/
│   ├── tokens.css         # Design system variables ONLY
│   ├── base.css          # Reset & typography
│   ├── components.css    # Reusable UI components
│   └── utilities.css     # Helper classes
├── assets/
│   ├── icons/            # SVG icons
│   └── images/           # Optimized images
└── docs/
    ├── BUILD_GUIDELINES.md # This file
    └── DESIGN_SYSTEM.md   # Visual design specifications
```

---

## 📊 **Data Architecture Patterns**

### **Schema-First Data Management**

#### **✅ TEMPLATE: Data Schema**
```javascript
// schemas.js - Copy this pattern for every data type
const Schemas = {
    // Template for any data object
    [DataTypeName]: {
        // Required fields
        id: { type: 'string', required: true, minLength: 1 },
        title: { type: 'string', required: true, minLength: 1, maxLength: 100 },
        
        // Optional fields with defaults
        status: { type: 'string', default: 'draft', enum: ['draft', 'active', 'archived'] },
        tags: { type: 'array', default: [], items: { type: 'string' } },
        
        // Nested objects
        metadata: {
            type: 'object',
            default: {},
            properties: {
                createdAt: { type: 'string', format: 'iso-date', required: true },
                updatedAt: { type: 'string', format: 'iso-date', required: true }
            }
        }
    }
};
```

#### **✅ TEMPLATE: Data Manager Module**
```javascript
// [feature]-manager.js - Copy this template for every feature
const FeatureManager = {
    // Storage configuration
    STORAGE_KEY: 'app_feature_v1',
    BACKUP_KEY: 'app_feature_v1_backup',
    
    // Core methods - ALWAYS include these 4
    async init() {
        try {
            await this.validateStorageHealth();
            await this.migrateDataIfNeeded();
            return true;
        } catch (error) {
            console.error('FeatureManager init failed:', error);
            return false;
        }
    },
    
    save(data) {
        // 1. Validate against schema
        const validation = Schemas.validate(data, Schemas.FeatureName);
        if (!validation.isValid) {
            throw new Error('Invalid data: ' + validation.errors.join(', '));
        }
        
        // 2. Create versioned wrapper
        const wrappedData = {
            version: '1.0.0',
            timestamp: new Date().toISOString(),
            data: validation.data // Use validated data with defaults
        };
        
        // 3. Save with error handling
        try {
            localStorage.setItem(this.STORAGE_KEY, JSON.stringify(wrappedData));
            this.createBackup(wrappedData);
            return true;
        } catch (error) {
            if (error.name === 'QuotaExceededError') {
                this.handleStorageQuota();
                return this.save(data); // Retry once
            }
            throw error;
        }
    },
    
    load() {
        try {
            const rawData = localStorage.getItem(this.STORAGE_KEY);
            if (!rawData) return null;
            
            const parsed = JSON.parse(rawData);
            
            // Version check and migration
            if (parsed.version !== '1.0.0') {
                return this.migrateData(parsed);
            }
            
            return parsed.data;
        } catch (error) {
            console.error('Load failed, trying backup:', error);
            return this.loadBackup();
        }
    },
    
    handleError(error, context = '') {
        console.error(`FeatureManager error [${context}]:`, error);
        
        // User-friendly error messages
        const userMessage = this.getUserFriendlyError(error);
        
        // Show error to user (implement your preferred method)
        if (typeof showUserError === 'function') {
            showUserError(userMessage);
        }
        
        // Track error for debugging
        this.trackError(error, context);
    },
    
    // Helper methods
    createBackup(data) {
        try {
            localStorage.setItem(this.BACKUP_KEY, JSON.stringify(data));
        } catch (error) {
            console.warn('Backup creation failed:', error);
        }
    },
    
    loadBackup() {
        try {
            const backup = localStorage.getItem(this.BACKUP_KEY);
            if (backup) {
                const parsed = JSON.parse(backup);
                console.warn('Loaded data from backup');
                return parsed.data;
            }
        } catch (error) {
            console.error('Backup load failed:', error);
        }
        return null;
    },
    
    handleStorageQuota() {
        // Clean up old data to free space
        this.cleanupOldData();
        console.warn('Storage quota exceeded, cleaned up old data');
    },
    
    getUserFriendlyError(error) {
        const errorMap = {
            'QuotaExceededError': 'Storage is full. Some old data was cleaned up.',
            'SecurityError': 'Storage access denied. Please check browser settings.',
            'SyntaxError': 'Data corruption detected. Restored from backup.'
        };
        return errorMap[error.name] || 'Something went wrong. Please try again.';
    }
};
```

### **Decision Matrix: Data Storage**

| Data Size | Complexity | Offline Need | Choose |
|-----------|------------|--------------|--------|
| < 5MB | Simple | Yes | localStorage + schemas |
| < 5MB | Complex | Yes | localStorage + validation |
| > 5MB | Any | Yes | IndexedDB + schemas |
| Any | Any | No | API + caching |

---

## 🎨 **CSS Architecture Patterns**

### **Design Token Structure**

#### **✅ TEMPLATE: Design Tokens (tokens.css)**
```css
:root {
    /* ================================
       DESIGN TOKENS - Technical Values Only
       ================================ */
    
    /* Colors - Semantic naming */
    --color-primary: #3B82F6;
    --color-primary-hover: #2563EB;
    --color-primary-light: #DBEAFE;
    --color-primary-dark: #1E40AF;
    
    --color-success: #10B981;
    --color-warning: #F59E0B;
    --color-error: #EF4444;
    --color-info: #6B7280;
    
    /* Neutrals */
    --color-white: #FFFFFF;
    --color-black: #000000;
    --color-gray-50: #F9FAFB;
    --color-gray-100: #F3F4F6;
    --color-gray-500: #6B7280;
    --color-gray-900: #111827;
    
    /* Typography Scale */
    --font-size-xs: 0.75rem;    /* 12px */
    --font-size-sm: 0.875rem;   /* 14px */
    --font-size-base: 1rem;     /* 16px */
    --font-size-lg: 1.125rem;   /* 18px */
    --font-size-xl: 1.25rem;    /* 20px */
    --font-size-2xl: 1.5rem;    /* 24px */
    --font-size-3xl: 2rem;      /* 32px */
    
    --font-weight-normal: 400;
    --font-weight-medium: 500;
    --font-weight-semibold: 600;
    --font-weight-bold: 700;
    
    /* Spacing - 4px base unit */
    --space-1: 4px;
    --space-2: 8px;
    --space-3: 12px;
    --space-4: 16px;
    --space-6: 24px;
    --space-8: 32px;
    --space-12: 48px;
    --space-16: 64px;
    
    /* Border radius */
    --radius-sm: 4px;
    --radius-md: 8px;
    --radius-lg: 12px;
    --radius-xl: 16px;
    --radius-full: 9999px;
    
    /* Shadows */
    --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
    --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
    --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
    
    /* Breakpoints (for JavaScript use) */
    --breakpoint-sm: 640px;
    --breakpoint-md: 768px;
    --breakpoint-lg: 1024px;
    --breakpoint-xl: 1280px;
}
```

### **CSS Organization Rules**

#### **✅ File Structure**
```css
/* 1. tokens.css - Design system variables */
/* 2. base.css - Reset, typography, basic elements */  
/* 3. components.css - Reusable UI components */
/* 4. utilities.css - Helper classes */
```

#### **✅ Component Structure Template**
```css
/* Base component */
.component-name {
    /* Use tokens for all values */
    background: var(--color-white);
    border-radius: var(--radius-md);
    padding: var(--space-4);
    
    /* Interactions */
    transition: all 0.2s ease-in-out;
}

/* Component states */
.component-name:hover {
    transform: translateY(-2px);
}

.component-name:focus {
    outline: 2px solid var(--color-primary);
    outline-offset: 2px;
}

/* Component variants */
.component-name--variant {
    background: var(--color-primary-light);
}

/* Component elements */
.component-name__element {
    font-size: var(--font-size-sm);
    color: var(--color-gray-500);
}

/* Responsive behavior */
@media (max-width: 640px) {
    .component-name {
        padding: var(--space-2);
    }
}
```

#### **❌ Anti-Patterns**
```css
/* ❌ DON'T: Hardcode values */
.button { background: #3B82F6; padding: 16px; }

/* ✅ DO: Use tokens */
.button { background: var(--color-primary); padding: var(--space-4); }

/* ❌ DON'T: Framework-dependent code */
.button { @apply bg-blue-500 text-white px-4 py-2; }

/* ✅ DO: Standard CSS with tokens */
.button { 
    background: var(--color-primary);
    color: var(--color-white);
    padding: var(--space-2) var(--space-4);
}

/* ❌ DON'T: Complex selectors */
.sidebar .nav ul li a:hover { }

/* ✅ DO: Component-based classes */
.nav-link:hover { }
```

---

## ⚡ **Performance Constraints**

### **Hard Limits - Never Exceed**

| Resource | Maximum | Measurement | Why |
|----------|---------|-------------|-----|
| JavaScript Bundle | 500KB | Gzipped total | Mobile performance |
| CSS Bundle | 100KB | Gzipped total | Render blocking |
| localStorage Usage | 8MB | Total across all keys | Browser limits |
| Individual Objects | 1MB | Single JSON object | Parsing performance |
| API Response Time | 3 seconds | Network timeout | User patience |
| Images | 200KB | Per image file | Mobile data usage |

### **Performance Monitoring**
```javascript
// Template: Performance budget checker
const checkPerformanceBudget = () => {
    // Check localStorage usage
    const storageSize = JSON.stringify(localStorage).length;
    const storageMB = (storageSize / 1024 / 1024).toFixed(2);
    
    if (storageMB > 8) {
        console.warn(`Storage usage: ${storageMB}MB (over 8MB limit)`);
        return false;
    }
    
    return true;
};

// Check bundle size in build process
const checkBundleSize = (filePath, maxSize) => {
    const stats = fs.statSync(filePath);
    const fileSizeKB = stats.size / 1024;
    
    if (fileSizeKB > maxSize) {
        throw new Error(`Bundle too large: ${fileSizeKB}KB (max: ${maxSize}KB)`);
    }
};
```

---

## 🛡️ **Error Handling Patterns**

### **✅ TEMPLATE: Error Boundary**
```javascript
// Wrap critical operations with error boundaries
const executeWithErrorBoundary = async (operation, operationName, options = {}) => {
    const { fallbackValue = null, retryCount = 0, maxRetries = 2 } = options;
    
    try {
        return await operation();
    } catch (error) {
        console.error(`Error in ${operationName}:`, error);
        
        // Retry for recoverable errors
        if (isRecoverableError(error) && retryCount < maxRetries) {
            await delay(1000 * (retryCount + 1));
            return executeWithErrorBoundary(operation, operationName, {
                ...options,
                retryCount: retryCount + 1
            });
        }
        
        // Log for debugging
        trackError(operationName, error);
        
        // Return fallback
        return fallbackValue;
    }
};

const isRecoverableError = (error) => {
    const recoverableTypes = [
        'NetworkError',
        'TimeoutError', 
        'QuotaExceededError',
        'AbortError'
    ];
    return recoverableTypes.includes(error.name);
};

const delay = (ms) => new Promise(resolve => setTimeout(resolve, ms));
```

### **User-Friendly Error Messages**
```javascript
const getUserFriendlyError = (technicalError) => {
    const errorMap = {
        'QuotaExceededError': 'Storage is full. Cleaned up old data to make room.',
        'SecurityError': 'Storage access denied. Please check browser settings.',
        'NetworkError': 'Connection issue. Please check your internet connection.',
        'TimeoutError': 'Operation took too long. Please try again.',
        'ValidationError': 'Please check your input and try again.',
        'SyntaxError': 'Data corruption detected. Restored from backup.'
    };
    
    return errorMap[technicalError.name] || 'Something went wrong. Please try again.';
};
```

---

## 🧪 **Testing Workflows**

### **Pre-Development Checklist**
- [ ] Schema defined in `schemas.js`
- [ ] Error scenarios documented
- [ ] Performance budget allocated
- [ ] Browser support requirements confirmed

### **Development Testing**
```javascript
// Test scenarios to verify for every feature
const testScenarios = [
    // Data integrity
    'localStorage quota exceeded',
    'Invalid JSON in localStorage',
    'Schema validation with invalid data',
    'Data corruption and recovery',
    
    // Network issues  
    'Network disconnected during operation',
    'API timeout handling',
    'Slow network conditions',
    
    // User scenarios
    'Invalid user inputs',
    'Missing required fields',
    'Concurrent operations'
];
```

### **Pre-Deployment Checklist**
- [ ] All modules load without console errors
- [ ] Performance budgets met (check with DevTools)
- [ ] Error boundaries catch and recover gracefully
- [ ] Schema validation works with invalid inputs
- [ ] Mobile responsiveness verified (320px, 768px, 1024px)
- [ ] Cross-browser testing completed
- [ ] Accessibility standards met (WCAG 2.1 AA minimum)

---

## 🎯 **UI Conformance Validation**

### **Design System Compliance Script**

#### **✅ TEMPLATE: UI Conformance Checker (ui-conformance.js)**
```javascript
// ui-conformance.js - Lightweight design system validation
const UIConformanceChecker = {
    
    // Configuration
    DESIGN_TOKENS_FILE: 'css/tokens.css',
    COMPLIANCE_RULES: {
        hardcodedValues: {
            colors: /#[0-9a-f]{3,6}/gi,
            spacing: /\d+(px|rem|em)/gi,
            fontSize: /font-size:\s*\d+(px|rem|em)/gi
        },
        accessibility: {
            minContrastRatio: 4.5,
            minTouchTarget: 44
        }
    },
    
    // Main validation function
    async validateCompliance(cssFiles = []) {
        console.log('🔍 Running UI conformance validation...');
        
        const results = {
            passed: true,
            violations: [],
            warnings: [],
            summary: {}
        };
        
        try {
            // 1. Check for hardcoded values
            const hardcodedViolations = await this.checkHardcodedValues(cssFiles);
            if (hardcodedViolations.length > 0) {
                results.passed = false;
                results.violations.push(...hardcodedViolations);
            }
            
            // 2. Validate color contrast (placeholder)
            const contrastViolations = await this.checkColorContrast();
            results.warnings.push(...contrastViolations);
            
            // 3. Check touch targets (placeholder)
            const touchTargetViolations = await this.checkTouchTargets();
            results.warnings.push(...touchTargetViolations);
            
            // 4. Validate design token usage
            const tokenViolations = await this.checkDesignTokenUsage(cssFiles);
            results.violations.push(...tokenViolations);
            
            results.summary = {
                violations: results.violations.length,
                warnings: results.warnings.length,
                filesChecked: cssFiles.length
            };
            
            this.reportResults(results);
            return results;
            
        } catch (error) {
            console.error('UI conformance check failed:', error);
            return { passed: false, error: error.message };
        }
    },
    
    // Check for hardcoded CSS values
    async checkHardcodedValues(cssFiles) {
        const violations = [];
        
        for (const file of cssFiles) {
            try {
                const cssContent = await this.readFile(file);
                const rules = this.COMPLIANCE_RULES.hardcodedValues;
                
                // Check for hardcoded colors
                const colorMatches = cssContent.match(rules.colors);
                if (colorMatches) {
                    violations.push({
                        type: 'hardcoded-color',
                        file: file,
                        violations: colorMatches,
                        message: 'Use CSS custom properties (--color-*) instead of hardcoded colors'
                    });
                }
                
                // Check for hardcoded spacing
                const spacingMatches = cssContent.match(rules.spacing);
                if (spacingMatches) {
                    const filteredSpacing = spacingMatches.filter(match => 
                        !match.includes('var(--') && !match.includes('0px')
                    );
                    if (filteredSpacing.length > 0) {
                        violations.push({
                            type: 'hardcoded-spacing',
                            file: file,
                            violations: filteredSpacing,
                            message: 'Use spacing tokens (--space-*) instead of hardcoded values'
                        });
                    }
                }
                
            } catch (error) {
                console.warn(`Could not read file ${file}:`, error.message);
            }
        }
        
        return violations;
    },
    
    // Placeholder: Color contrast validation
    async checkColorContrast() {
        // TODO: Implement automated color contrast checking
        // This would analyze computed styles and measure contrast ratios
        return []; // Return contrast violations
    },
    
    // Placeholder: Touch target validation
    async checkTouchTargets() {
        // TODO: Implement touch target size checking
        // This would check all interactive elements for 44px+ targets
        return []; // Return touch target violations
    },
    
    // Placeholder: Design token usage validation
    async checkDesignTokenUsage(cssFiles) {
        const violations = [];
        // TODO: Cross-reference CSS usage with DESIGN_SYSTEM.md tokens
        // Ensure all values come from approved design token list
        return violations;
    },
    
    // File reading utility (Node.js or browser compatible)
    async readFile(filePath) {
        if (typeof require !== 'undefined') {
            // Node.js environment
            const fs = require('fs').promises;
            return await fs.readFile(filePath, 'utf8');
        } else {
            // Browser environment
            const response = await fetch(filePath);
            return await response.text();
        }
    },
    
    // Report validation results
    reportResults(results) {
        console.log('\n🎯 UI Conformance Report');
        console.log('========================');
        
        if (results.passed) {
            console.log('✅ All UI conformance checks passed!');
        } else {
            console.log(`❌ ${results.violations.length} violations found`);
        }
        
        if (results.warnings.length > 0) {
            console.log(`⚠️  ${results.warnings.length} warnings to review`);
        }
        
        // Detail violations
        results.violations.forEach(violation => {
            console.log(`\n🔸 ${violation.type} in ${violation.file}`);
            console.log(`   ${violation.message}`);
            if (violation.violations) {
                violation.violations.forEach(v => console.log(`   - ${v}`));
            }
        });
        
        console.log('\n📊 Summary:');
        console.log(`   Files checked: ${results.summary.filesChecked}`);
        console.log(`   Violations: ${results.summary.violations}`);
        console.log(`   Warnings: ${results.summary.warnings}`);
    }
};

// CLI usage (Node.js)
if (typeof module !== 'undefined' && module.exports) {
    module.exports = UIConformanceChecker;
    
    // Run if called directly
    if (require.main === module) {
        const cssFiles = process.argv.slice(2) || ['css/components.css', 'css/base.css'];
        UIConformanceChecker.validateCompliance(cssFiles);
    }
}

// Browser usage
if (typeof window !== 'undefined') {
    window.UIConformanceChecker = UIConformanceChecker;
}
```

### **Usage Instructions**

#### **Command Line (Development)**
```bash
# Check specific CSS files
node ui-conformance.js css/components.css css/base.css

# Check all CSS files (requires glob)
node -e "
const glob = require('glob');
const checker = require('./ui-conformance.js');
const files = glob.sync('css/*.css');
checker.validateCompliance(files);
"
```

#### **Integration with Build Process**
```javascript
// In your build script
const UIConformanceChecker = require('./ui-conformance.js');

const buildProcess = async () => {
    // ... other build steps
    
    // UI Conformance validation
    const cssFiles = ['css/components.css', 'css/base.css', 'css/utilities.css'];
    const conformanceResults = await UIConformanceChecker.validateCompliance(cssFiles);
    
    if (!conformanceResults.passed) {
        console.error('❌ Build failed: UI conformance violations detected');
        process.exit(1); // Block build on violations
    }
    
    console.log('✅ UI conformance validation passed');
    // ... continue build
};
```

#### **Integration with Commands**
```javascript
// Add to checkpoint.md and prepare-deploy.md workflows
// This script will be called automatically during:
// - /checkpoint command (before commit)
// - /prepare-deploy command (before deployment)
// - CI/CD pipeline (automated builds)
```

### **Future Enhancements**

**Phase 1 (Current)**: Hardcoded value detection
**Phase 2**: Color contrast automation with WCAG validation
**Phase 3**: Touch target size measurement using DOM analysis
**Phase 4**: Design token cross-reference with DESIGN_SYSTEM.md
**Phase 5**: Visual regression testing integration

---

## 🚀 **Quick Reference Card**

### **Adding New Feature - 7 Steps**
1. **Define schema** in `schemas.js`
2. **Create manager** using template in `js/[feature]-manager.js`
3. **Add validation** in manager's save/load methods
4. **Create CSS components** using token structure
5. **Wrap operations** in error boundaries
6. **Test error scenarios** from testing list
7. **Verify performance budget** not exceeded

### **Emergency Debugging Checklist**
1. **Check browser console** for JavaScript errors
2. **Inspect localStorage** data integrity  
3. **Test with clean localStorage** (clear all data)
4. **Check network tab** for failed requests
5. **Validate data** against schemas manually
6. **Try backup restoration** if available
7. **Check performance metrics** in DevTools

### **Common Technical Fixes**
```javascript
// localStorage quota exceeded
const freeUpStorage = () => {
    // Remove old data, compress existing data
    const keys = Object.keys(localStorage);
    keys.forEach(key => {
        if (key.includes('old_') || key.includes('temp_')) {
            localStorage.removeItem(key);
        }
    });
};

// Invalid JSON recovery
const safeJSONParse = (data, fallback = null) => {
    try {
        return JSON.parse(data);
    } catch {
        console.warn('Invalid JSON, using fallback');
        return fallback;
    }
};

// Schema validation failure
const applyDefaults = (data, schema) => {
    const result = { ...data };
    Object.entries(schema).forEach(([key, rules]) => {
        if (rules.default !== undefined && result[key] === undefined) {
            result[key] = rules.default;
        }
    });
    return result;
};
```

---

## 🔒 **Security Guidelines**

### **Data Security Rules**
- **Never store** sensitive data (passwords, API keys, personal info) in localStorage
- **Always validate** user inputs before processing
- **Sanitize** HTML content before displaying to DOM
- **Use HTTPS** for all external API requests
- **Implement CSP headers** when possible

### **Code Security Patterns**
```javascript
// Input validation
const validateInput = (input, type, maxLength = 1000) => {
    if (typeof input !== type) {
        throw new Error(`Expected ${type}, got ${typeof input}`);
    }
    
    if (type === 'string' && input.length > maxLength) {
        throw new Error(`Input too long: ${input.length} chars (max: ${maxLength})`);
    }
    
    return input.trim();
};

// HTML sanitization
const sanitizeHTML = (content) => {
    const div = document.createElement('div');
    div.textContent = content; // Prevents XSS
    return div.innerHTML;
};

// Safe external requests
const safeRequest = async (url, options = {}) => {
    if (!url.startsWith('https://')) {
        throw new Error('Only HTTPS URLs allowed');
    }
    
    const controller = new AbortController();
    const timeoutId = setTimeout(() => controller.abort(), 10000);
    
    try {
        const response = await fetch(url, {
            ...options,
            signal: controller.signal
        });
        clearTimeout(timeoutId);
        return response;
    } catch (error) {
        clearTimeout(timeoutId);
        throw error;
    }
};
```

---

## 📖 **Browser Support Matrix**

| Browser | Version | Support Level | Notes |
|---------|---------|---------------|-------|
| Chrome | 90+ | Full support | Primary development target |
| Firefox | 88+ | Full support | Test regularly |
| Safari | 14+ | Full support | Test on iOS Safari |
| Edge | 90+ | Full support | Chromium-based |
| IE | Any | Not supported | Use feature detection |

### **Feature Detection Pattern**
```javascript
// Use feature detection, not browser detection
const hasLocalStorage = () => {
    try {
        const test = '__storage_test__';
        localStorage.setItem(test, test);
        localStorage.removeItem(test);
        return true;
    } catch {
        return false;
    }
};

const hasModernJavaScript = () => {
    return typeof Promise !== 'undefined' && 
           typeof fetch !== 'undefined' &&
           typeof Object.assign !== 'undefined';
};
```

---

## 🔧 **Development Environment**

### **Required Tools**
- **Node.js** 16+ for build tools
- **Modern browser** with DevTools for debugging  
- **HTTP server** (`npx http-server`) for local development
- **Code editor** with JavaScript/CSS support

### **Recommended Setup**
```bash
# Local development server
npx http-server -p 3000 -c-1

# Performance testing
# Use Chrome DevTools Lighthouse for auditing
# Use Network tab to check bundle sizes
# Use Application tab to inspect localStorage
```

### **Build Process Checklist**
- [ ] Minify JavaScript and CSS for production
- [ ] Optimize images (WebP conversion where supported)
- [ ] Generate source maps for debugging
- [ ] Run performance budget checks
- [ ] Validate HTML/CSS syntax

---

## 📋 **Related Documentation**

- **DESIGN_SYSTEM.md** - Component styles, visual specifications, brand colors
- **COMPONENT_LIBRARY.md** - Interactive examples and copy-paste components
- **API_DOCUMENTATION.md** - External service integrations and endpoints
- **DEPLOYMENT_GUIDE.md** - Production deployment and hosting requirements

---

**Remember**: These are battle-tested patterns, not rigid rules. Adapt based on your specific requirements, but always maintain the core principles of data integrity, performance, and error resilience.

---

*Last Updated: Based on real-world application architecture and development experience*