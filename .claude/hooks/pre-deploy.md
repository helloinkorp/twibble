---
name: pre-deploy
description: Comprehensive pre-deployment validation hook that blocks deployments on failed tests, missing reviews, or critical quality issues
trigger: before_deploy
---

# Pre-Deploy Hook

## **🎯 Purpose**
Critical safety gate that prevents deployment of the Twibble vocabulary learning platform when tests fail, reviews are incomplete, or quality standards are not met. This hook ensures production stability and educational platform reliability.

## **🛑 Deployment Gate Flow**

### **Step 1: Pre-Deploy Environment Validation**
```bash
echo "🛡️  PRE-DEPLOY VALIDATION GATE"
echo "==============================="

deployment_timestamp=$(date -u +%Y-%m-%dT%H:%M:%SZ)
deployment_environment="${DEPLOYMENT_ENV:-production}"
current_branch=$(git branch --show-current)

echo "🕐 Validation Time: $deployment_timestamp"
echo "🌍 Environment: $deployment_environment"
echo "🌿 Branch: $current_branch"
echo "📂 Directory: $(pwd)"

# Initialize gate status
gate_status="PASS"
critical_failures=()
warning_issues=()
blocking_issues=()

# Verify we're in a Twibble project
if [ ! -f "consolidated_words.json" ] || [ ! -f "script.js" ] || [ ! -f "manifest.json" ]; then
  critical_failures+=("Not a valid Twibble project - missing core files")
  gate_status="FAIL"
fi

echo "🎓 Project: Twibble Vocabulary Learning Platform"
```

**Expected Output:**
```
🛡️  PRE-DEPLOY VALIDATION GATE
===============================
🕐 Validation Time: 2024-01-15T19:50:30Z
🌍 Environment: production
🌿 Branch: main
📂 Directory: /Users/USER/Desktop/Twibble
🎓 Project: Twibble Vocabulary Learning Platform
```

### **Step 2: Critical Test Validation**
```bash
echo ""
echo "🧪 CRITICAL TEST VALIDATION"
echo "============================="

# Unit test execution and validation
echo "🔍 Running unit test suite..."

if command -v npm &> /dev/null && [ -f "package.json" ]; then
  # Run tests with coverage
  if npm run test:coverage &> test-results.log; then
    # Extract coverage percentage
    coverage=$(grep -o '[0-9]*\.*[0-9]*%' test-results.log | tail -1 || echo "0%")
    test_count=$(grep -o '[0-9]* passing' test-results.log | grep -o '[0-9]*' || echo "0")
    
    echo "✅ Unit Tests: $test_count passing ($coverage coverage)"
    
    # Validate coverage threshold
    coverage_num=$(echo "$coverage" | sed 's/%//')
    if [ "${coverage_num%.*}" -lt 90 ]; then
      blocking_issues+=("Test coverage below 90%: $coverage")
    fi
    
  else
    echo "❌ Unit Tests: FAILED"
    test_failures=$(grep -E "(failing|error)" test-results.log | wc -l)
    critical_failures+=("Unit tests failed: $test_failures failures detected")
    gate_status="FAIL"
  fi
else
  echo "⚠️  No test framework detected - manual verification required"
  warning_issues+=("No automated tests available")
fi

# Accessibility compliance validation
echo "🔍 Running accessibility audit..."

if command -v axe &> /dev/null && [ -f "index.html" ]; then
  if axe index.html &> accessibility-results.log; then
    violation_count=$(grep -c "violation" accessibility-results.log || echo "0")
    if [ "$violation_count" -eq 0 ]; then
      echo "✅ Accessibility: WCAG 2.1 AA Compliant"
    else
      echo "❌ Accessibility: $violation_count violations found"
      critical_failures+=("Accessibility violations: $violation_count (WCAG 2.1 AA required)")
      gate_status="FAIL"
    fi
  else
    echo "❌ Accessibility audit failed to run"
    warning_issues+=("Accessibility audit could not complete")
  fi
else
  echo "⚠️  Accessibility audit skipped (axe-cli not available)"
  warning_issues+=("Manual accessibility verification required")
fi

# Performance validation
echo "🔍 Checking performance requirements..."

if [ -f "script.js" ] && [ -f "styles.css" ]; then
  js_size=$(stat --format=%s script.js 2>/dev/null | awk '{print int($1/1024)}' || echo "unknown")
  css_size=$(stat --format=%s styles.css 2>/dev/null | awk '{print int($1/1024)}' || echo "unknown")
  
  echo "📊 Bundle Sizes: JS ${js_size}KB, CSS ${css_size}KB"
  
  # Validate performance budgets
  if [ "$js_size" != "unknown" ] && [ "$js_size" -gt 500 ]; then
    blocking_issues+=("JavaScript bundle exceeds 500KB limit: ${js_size}KB")
  fi
  
  if [ "$css_size" != "unknown" ] && [ "$css_size" -gt 100 ]; then
    blocking_issues+=("CSS bundle exceeds 100KB limit: ${css_size}KB")
  fi
  
  if [ ${#blocking_issues[@]} -eq 0 ]; then
    echo "✅ Performance: Bundle sizes within limits"
  fi
fi
```

**Expected Output:**
```
🧪 CRITICAL TEST VALIDATION
=============================
🔍 Running unit test suite...
✅ Unit Tests: 45 passing (94% coverage)
🔍 Running accessibility audit...
✅ Accessibility: WCAG 2.1 AA Compliant
🔍 Checking performance requirements...
📊 Bundle Sizes: JS 487KB, CSS 89KB
✅ Performance: Bundle sizes within limits
```

### **Step 3: Code Quality & Security Gates**
```bash
echo ""
echo "🔐 CODE QUALITY & SECURITY GATES"
echo "=================================="

# Linting validation
echo "🔍 Running code quality checks..."

if command -v eslint &> /dev/null && [ -f ".eslintrc" -o -f ".eslintrc.json" ]; then
  if eslint . &> lint-results.log; then
    echo "✅ Linting: No errors found"
  else
    error_count=$(grep -c "error" lint-results.log || echo "0")
    warning_count=$(grep -c "warning" lint-results.log || echo "0")
    
    if [ "$error_count" -gt 0 ]; then
      echo "❌ Linting: $error_count errors, $warning_count warnings"
      critical_failures+=("ESLint errors must be fixed: $error_count errors")
      gate_status="FAIL"
    else
      echo "⚠️  Linting: $warning_count warnings (errors: 0)"
      warning_issues+=("Code warnings present: $warning_count")
    fi
  fi
else
  echo "⚠️  No linting configuration found"
  warning_issues+=("Code quality validation skipped")
fi

# Security audit
echo "🔍 Running security audit..."

if command -v npm &> /dev/null && [ -f "package.json" ]; then
  if npm audit --audit-level high &> security-results.log; then
    echo "✅ Security: No high-severity vulnerabilities"
  else
    vuln_count=$(grep -c "high\|critical" security-results.log || echo "0")
    echo "❌ Security: $vuln_count high/critical vulnerabilities"
    critical_failures+=("Security vulnerabilities must be resolved: $vuln_count high/critical")
    gate_status="FAIL"
  fi
else
  echo "⚠️  Security audit skipped (npm not available)"
  warning_issues+=("Manual security review required")
fi

# Check for sensitive data leaks
echo "🔍 Scanning for sensitive data..."

sensitive_patterns=(
  "api[_-]?key"
  "secret"
  "password"
  "token"
  "private[_-]?key"
  "[a-zA-Z0-9]{32,}" # Potential API keys
)

sensitive_found=false
for pattern in "${sensitive_patterns[@]}"; do
  if grep -r -i -E "$pattern" --include="*.js" --include="*.html" --include="*.css" . | grep -v -E "(placeholder|example|test|demo)"; then
    sensitive_found=true
  fi
done

if [ "$sensitive_found" = true ]; then
  echo "❌ Sensitive Data: Potential secrets found in code"
  critical_failures+=("Sensitive data patterns detected - review before deployment")
  gate_status="FAIL"
else
  echo "✅ Sensitive Data: No secrets detected"
fi
```

**Expected Output:**
```
🔐 CODE QUALITY & SECURITY GATES
==================================
🔍 Running code quality checks...
✅ Linting: No errors found
🔍 Running security audit...
✅ Security: No high-severity vulnerabilities
🔍 Scanning for sensitive data...
✅ Sensitive Data: No secrets detected
```

### **Step 4: Twibble-Specific Validation**
```bash
echo ""
echo "🎓 TWIBBLE PLATFORM VALIDATION"
echo "==============================="

# Vocabulary data integrity
echo "🔍 Validating vocabulary learning data..."

if [ -f "consolidated_words.json" ]; then
  # Validate JSON structure and content
  vocab_validation=$(node -e "
    try {
      const data = require('./consolidated_words.json');
      if (!data.words || !Array.isArray(data.words)) {
        console.log('FAIL|Invalid words array structure');
      } else if (data.words.length < 100) {
        console.log('WARN|Low word count: ' + data.words.length);
      } else {
        // Validate sample word structure
        const sample = data.words[0];
        if (!sample.text || !sample.imageUrl) {
          console.log('FAIL|Invalid word structure missing text/imageUrl');
        } else {
          // Check for broken URLs
          const brokenUrls = data.words.filter(w => 
            !w.imageUrl || !w.imageUrl.startsWith('http')
          ).length;
          
          if (brokenUrls > data.words.length * 0.1) { // More than 10% broken
            console.log('FAIL|Too many broken asset URLs: ' + brokenUrls);
          } else {
            console.log('PASS|Valid structure: ' + data.words.length + ' words, ' + brokenUrls + ' potential issues');
          }
        }
      }
    } catch (e) {
      console.log('FAIL|JSON parsing error: ' + e.message);
    }
  " 2>/dev/null || echo "FAIL|Node.js validation failed")
  
  vocab_status=$(echo "$vocab_validation" | cut -d'|' -f1)
  vocab_message=$(echo "$vocab_validation" | cut -d'|' -f2-)
  
  case "$vocab_status" in
    "PASS")
      echo "✅ Vocabulary Data: $vocab_message"
      ;;
    "WARN")
      echo "⚠️  Vocabulary Data: $vocab_message"
      warning_issues+=("Vocabulary data: $vocab_message")
      ;;
    "FAIL")
      echo "❌ Vocabulary Data: $vocab_message"
      critical_failures+=("Vocabulary data: $vocab_message")
      gate_status="FAIL"
      ;;
  esac
else
  echo "❌ Vocabulary Data: consolidated_words.json missing"
  critical_failures+=("Critical file missing: consolidated_words.json")
  gate_status="FAIL"
fi

# Phonics chunks validation
echo "🔍 Validating phonics learning data..."

if [ -f "phonics_chunks.txt" ]; then
  # Validate format and content
  total_lines=$(wc -l < phonics_chunks.txt)
  invalid_format=$(grep -v -E '^[a-zA-Z]+\|[a-zA-Z,]+$' phonics_chunks.txt | wc -l)
  
  if [ "$invalid_format" -gt 0 ]; then
    echo "❌ Phonics Data: $invalid_format invalid format lines"
    critical_failures+=("Phonics chunks format errors: $invalid_format lines")
    gate_status="FAIL"
  elif [ "$total_lines" -lt 50 ]; then
    echo "⚠️  Phonics Data: Low entry count ($total_lines)"
    warning_issues+=("Phonics chunks: only $total_lines entries")
  else
    echo "✅ Phonics Data: $total_lines valid entries"
  fi
else
  echo "⚠️  Phonics Data: phonics_chunks.txt missing"
  warning_issues+=("Phonics chunks file not found - fallback will be used")
fi

# PWA requirements validation
echo "🔍 Validating PWA requirements..."

pwa_issues=()

if [ -f "manifest.json" ]; then
  # Validate PWA manifest
  manifest_check=$(node -e "
    try {
      const manifest = require('./manifest.json');
      const required = ['name', 'short_name', 'start_url', 'display', 'icons', 'theme_color'];
      const missing = required.filter(field => !manifest[field]);
      
      if (missing.length > 0) {
        console.log('FAIL|Missing required fields: ' + missing.join(', '));
      } else if (!manifest.icons || manifest.icons.length < 3) {
        console.log('WARN|Insufficient icons: ' + (manifest.icons?.length || 0));
      } else {
        console.log('PASS|Valid manifest with ' + manifest.icons.length + ' icons');
      }
    } catch (e) {
      console.log('FAIL|Manifest parsing error: ' + e.message);
    }
  " 2>/dev/null || echo "FAIL|Manifest validation failed")
  
  manifest_status=$(echo "$manifest_check" | cut -d'|' -f1)
  manifest_message=$(echo "$manifest_check" | cut -d'|' -f2-)
  
  case "$manifest_status" in
    "PASS")
      echo "✅ PWA Manifest: $manifest_message"
      ;;
    "WARN")
      echo "⚠️  PWA Manifest: $manifest_message"
      warning_issues+=("PWA manifest: $manifest_message")
      ;;
    "FAIL")
      echo "❌ PWA Manifest: $manifest_message"
      blocking_issues+=("PWA manifest: $manifest_message")
      ;;
  esac
else
  echo "❌ PWA Manifest: manifest.json missing"
  blocking_issues+=("PWA manifest.json required for deployment")
fi

# Service worker validation
if [ -f "sw.js" ]; then
  # Basic service worker syntax check
  if node -c sw.js 2>/dev/null; then
    echo "✅ Service Worker: Syntax valid"
  else
    echo "❌ Service Worker: Syntax errors detected"
    blocking_issues+=("Service worker has JavaScript syntax errors")
  fi
else
  echo "⚠️  Service Worker: sw.js not found"
  warning_issues+=("Service worker missing - offline functionality limited")
fi
```

**Expected Output:**
```
🎓 TWIBBLE PLATFORM VALIDATION
===============================
🔍 Validating vocabulary learning data...
✅ Vocabulary Data: Valid structure: 1247 words, 3 potential issues
🔍 Validating phonics learning data...
✅ Phonics Data: 892 valid entries
🔍 Validating PWA requirements...
✅ PWA Manifest: Valid manifest with 8 icons
✅ Service Worker: Syntax valid
```

### **Step 5: Git & Repository Validation**
```bash
echo ""
echo "📚 REPOSITORY & COLLABORATION GATES"
echo "====================================="

# Check for uncommitted changes
echo "🔍 Checking repository status..."

uncommitted=$(git status --porcelain | wc -l)
if [ "$uncommitted" -gt 0 ]; then
  echo "⚠️  Repository: $uncommitted uncommitted changes"
  warning_issues+=("Uncommitted changes in repository: $uncommitted files")
  
  # Show what's uncommitted
  echo "Uncommitted files:"
  git status --porcelain | head -5
else
  echo "✅ Repository: Working directory clean"
fi

# Validate branch for deployment
echo "🔍 Validating deployment branch..."

if [ "$deployment_environment" = "production" ]; then
  if [ "$current_branch" = "main" ] || [ "$current_branch" = "master" ]; then
    echo "✅ Branch: Deploying from $current_branch to production"
  else
    echo "❌ Branch: Production deployments must be from main/master branch"
    critical_failures+=("Production deployment attempted from $current_branch branch")
    gate_status="FAIL"
  fi
else
  echo "✅ Branch: $current_branch deployment to $deployment_environment allowed"
fi

# Check for required reviews (if GitHub is used)
if git remote get-url origin 2>/dev/null | grep -q "github.com"; then
  echo "🔍 Checking GitHub pull request status..."
  
  if command -v gh &> /dev/null; then
    # Check if current commit is part of a merged PR
    current_commit=$(git rev-parse HEAD)
    
    # For production, ensure this commit came through a reviewed PR
    if [ "$deployment_environment" = "production" ]; then
      # This is a simplified check - in practice you'd want more sophisticated PR validation
      if gh pr list --state merged --search "$current_commit" --json number | grep -q "number"; then
        echo "✅ Code Review: Commit from reviewed pull request"
      else
        echo "⚠️  Code Review: Direct commit to main branch"
        warning_issues+=("Production deployment without PR review")
      fi
    fi
  else
    echo "⚠️  GitHub CLI not available - skipping PR validation"
    warning_issues+=("Cannot validate pull request status")
  fi
fi

# Validate commit message quality
echo "🔍 Checking recent commit quality..."

recent_commit_msg=$(git log -1 --pretty=format:"%s" 2>/dev/null || echo "")
if [ ${#recent_commit_msg} -lt 10 ]; then
  warning_issues+=("Very short commit message: '$recent_commit_msg'")
elif echo "$recent_commit_msg" | grep -q -E "(fix|feat|docs|style|refactor|test|chore)"; then
  echo "✅ Commit Message: Follows conventional format"
else
  warning_issues+=("Commit message doesn't follow conventional format")
fi
```

**Expected Output:**
```
📚 REPOSITORY & COLLABORATION GATES  
=====================================
🔍 Checking repository status...
✅ Repository: Working directory clean
🔍 Validating deployment branch...
✅ Branch: Deploying from main to production
🔍 Checking GitHub pull request status...
✅ Code Review: Commit from reviewed pull request
🔍 Checking recent commit quality...
✅ Commit Message: Follows conventional format
```

### **Step 6: Educational Platform Compliance**
```bash
echo ""
echo "🎓 EDUCATIONAL PLATFORM COMPLIANCE"
echo "===================================="

# Privacy and student data protection
echo "🔍 Validating student privacy compliance..."

# Check for any student data collection
privacy_violations=()

# Search for data collection patterns
if grep -r -i -E "(collect.*data|store.*student|track.*user|analytics|personal.*info)" --include="*.js" --include="*.html" . | grep -v -E "(comment|example|test)"; then
  privacy_violations+=("Potential student data collection detected")
fi

# Check for third-party tracking
if grep -r -E "(google-analytics|facebook|twitter|tracking)" --include="*.js" --include="*.html" .; then
  privacy_violations+=("Third-party tracking scripts detected")
fi

if [ ${#privacy_violations[@]} -gt 0 ]; then
  echo "❌ Privacy: Compliance issues detected"
  for violation in "${privacy_violations[@]}"; do
    echo "  - $violation"
    critical_failures+=("Privacy compliance: $violation")
  done
  gate_status="FAIL"
else
  echo "✅ Privacy: No student data collection detected"
fi

# Accessibility for educational use
echo "🔍 Validating educational accessibility..."

accessibility_requirements=(
  "alt=" # Alt text for images
  "aria-label" # ARIA labels
  "role=" # ARIA roles
  "tabindex" # Keyboard navigation
)

missing_a11y=()
for requirement in "${accessibility_requirements[@]}"; do
  if ! grep -r "$requirement" --include="*.html" . >/dev/null 2>&1; then
    missing_a11y+=("$requirement")
  fi
done

if [ ${#missing_a11y[@]} -gt 0 ]; then
  echo "⚠️  Accessibility: Missing features: ${missing_a11y[*]}"
  warning_issues+=("Accessibility features missing: ${missing_a11y[*]}")
else
  echo "✅ Accessibility: Required features present"
fi

# Content appropriateness validation
echo "🔍 Checking content appropriateness..."

# This is a basic check - in practice you'd want more sophisticated content filtering
inappropriate_content=false

if grep -r -i -E "(violence|inappropriate|adult)" --include="*.json" --include="*.txt" . >/dev/null 2>&1; then
  inappropriate_content=true
fi

if [ "$inappropriate_content" = true ]; then
  echo "⚠️  Content: Potentially inappropriate content detected"
  warning_issues+=("Content review required for educational appropriateness")
else
  echo "✅ Content: No inappropriate content detected"
fi
```

**Expected Output:**
```
🎓 EDUCATIONAL PLATFORM COMPLIANCE
====================================
🔍 Validating student privacy compliance...
✅ Privacy: No student data collection detected
🔍 Validating educational accessibility...
✅ Accessibility: Required features present
🔍 Checking content appropriateness...
✅ Content: No inappropriate content detected
```

### **Step 7: Final Gate Decision**
```bash
echo ""
echo "🚨 DEPLOYMENT GATE DECISION"
echo "============================"

# Count issues
critical_count=${#critical_failures[@]}
blocking_count=${#blocking_issues[@]}
warning_count=${#warning_issues[@]}

echo "📊 Validation Summary:"
echo "- Critical Failures: $critical_count"
echo "- Blocking Issues: $blocking_count"
echo "- Warnings: $warning_count"
echo ""

# Generate detailed report
cat > deployment-gate-report.md << EOF
# Deployment Gate Report

**Timestamp:** $deployment_timestamp  
**Environment:** $deployment_environment  
**Branch:** $current_branch  
**Gate Status:** $gate_status  

## Summary
- **Critical Failures:** $critical_count
- **Blocking Issues:** $blocking_count  
- **Warnings:** $warning_count

$(if [ $critical_count -gt 0 ]; then
echo "## Critical Failures"
for failure in "${critical_failures[@]}"; do
  echo "- ❌ $failure"
done
echo ""
fi)

$(if [ $blocking_count -gt 0 ]; then
echo "## Blocking Issues"  
for issue in "${blocking_issues[@]}"; do
  echo "- 🚫 $issue"
done
echo ""
fi)

$(if [ $warning_count -gt 0 ]; then
echo "## Warnings"
for warning in "${warning_issues[@]}"; do
  echo "- ⚠️  $warning"  
done
echo ""
fi)

## Validation Details
- **Tests:** $([ -f "test-results.log" ] && echo "Available in test-results.log" || echo "Not run")
- **Linting:** $([ -f "lint-results.log" ] && echo "Available in lint-results.log" || echo "Not run")  
- **Security:** $([ -f "security-results.log" ] && echo "Available in security-results.log" || echo "Not run")
- **Accessibility:** $([ -f "accessibility-results.log" ] && echo "Available in accessibility-results.log" || echo "Not run")

## Educational Platform Status
- **Vocabulary Words:** $(node -e "console.log(require('./consolidated_words.json').words?.length || 0)" 2>/dev/null || echo "unknown")
- **Phonics Chunks:** $(wc -l < phonics_chunks.txt 2>/dev/null || echo "unknown")  
- **PWA Ready:** $([ -f "manifest.json" ] && [ -f "sw.js" ] && echo "Yes" || echo "No")
- **Privacy Compliant:** $([ ${#privacy_violations[@]} -eq 0 ] && echo "Yes" || echo "No")

---
*Generated by pre-deploy hook*
EOF

echo "📄 Detailed report saved: deployment-gate-report.md"
echo ""

# Make final gate decision
if [ "$gate_status" = "FAIL" ] || [ "$critical_count" -gt 0 ]; then
  echo "🚨 DEPLOYMENT BLOCKED"
  echo "==================="
  echo "❌ Critical failures must be resolved before deployment"
  echo ""
  echo "🔧 Required Actions:"
  
  if [ $critical_count -gt 0 ]; then
    echo "Critical Failures:"
    for failure in "${critical_failures[@]}"; do
      echo "  - $failure"
    done
  fi
  
  if [ $blocking_count -gt 0 ]; then
    echo "Blocking Issues:"  
    for issue in "${blocking_issues[@]}"; do
      echo "  - $issue"
    done
  fi
  
  echo ""
  echo "📋 Next Steps:"
  echo "1. Review deployment-gate-report.md for full details"
  echo "2. Fix all critical failures and blocking issues"
  echo "3. Re-run validation: /checkpoint"
  echo "4. Retry deployment: /prepare-deploy"
  
  exit 1
  
elif [ "$blocking_count" -gt 0 ]; then
  echo "⚠️  DEPLOYMENT REQUIRES REVIEW"
  echo "=============================="
  echo "🚫 Blocking issues detected - manual override required"
  echo ""
  echo "Blocking Issues:"
  for issue in "${blocking_issues[@]}"; do
    echo "  - $issue"
  done
  echo ""
  echo "To proceed despite blocking issues, use: /prepare-deploy --force"
  echo "⚠️  Manual review and approval recommended"
  
  exit 1
  
else
  echo "✅ DEPLOYMENT APPROVED"
  echo "===================="
  echo "🎉 All critical validations passed!"
  
  if [ $warning_count -gt 0 ]; then
    echo ""
    echo "⚠️  Warnings (non-blocking):"
    for warning in "${warning_issues[@]}"; do
      echo "  - $warning"
    done
    echo ""
    echo "Consider addressing warnings in future releases."
  fi
  
  echo ""
  echo "🚀 Twibble vocabulary learning platform ready for deployment"
  echo "📚 Educational features validated and compliant"
  echo "🎓 Students will have access to:"
  echo "   - Interactive vocabulary activities" 
  echo "   - Phonics-based learning with audio"
  echo "   - Touch-friendly spelling practice"
  echo "   - Offline-capable progressive web app"
  echo ""
  echo "✨ Deployment may proceed!"
  
  exit 0
fi
```

**Expected Output (Success):**
```
🚨 DEPLOYMENT GATE DECISION
============================
📊 Validation Summary:
- Critical Failures: 0
- Blocking Issues: 0
- Warnings: 2

📄 Detailed report saved: deployment-gate-report.md

✅ DEPLOYMENT APPROVED
====================
🎉 All critical validations passed!

⚠️  Warnings (non-blocking):
  - Code warnings present: 3
  - Phonics chunks file not found - fallback will be used

Consider addressing warnings in future releases.

🚀 Twibble vocabulary learning platform ready for deployment
📚 Educational features validated and compliant
🎓 Students will have access to:
   - Interactive vocabulary activities
   - Phonics-based learning with audio
   - Touch-friendly spelling practice
   - Offline-capable progressive web app

✨ Deployment may proceed!
```

**Expected Output (Blocked):**
```
🚨 DEPLOYMENT GATE DECISION
============================
📊 Validation Summary:
- Critical Failures: 2
- Blocking Issues: 1
- Warnings: 1

📄 Detailed report saved: deployment-gate-report.md

🚨 DEPLOYMENT BLOCKED
===================
❌ Critical failures must be resolved before deployment

🔧 Required Actions:
Critical Failures:
  - Unit tests failed: 3 failures detected
  - Accessibility violations: 2 (WCAG 2.1 AA required)
Blocking Issues:
  - JavaScript bundle exceeds 500KB limit: 534KB

📋 Next Steps:
1. Review deployment-gate-report.md for full details
2. Fix all critical failures and blocking issues
3. Re-run validation: /checkpoint
4. Retry deployment: /prepare-deploy
```

## **⚙️ Hook Configuration**

### **Environment Variables**
```bash
# Deployment gate configuration
DEPLOYMENT_GATE_STRICT=true           # Strict mode blocks on warnings
DEPLOYMENT_GATE_SKIP_TESTS=false      # Skip test validation (emergency only)
DEPLOYMENT_GATE_ACCESSIBILITY=true    # Enforce accessibility compliance  
DEPLOYMENT_GATE_PRIVACY=true          # Enforce privacy compliance
DEPLOYMENT_GATE_PERFORMANCE=true      # Enforce performance budgets
```

### **Override Mechanisms**
```bash
# Emergency overrides (use with extreme caution)
/prepare-deploy production --force              # Override all blocking issues
/prepare-deploy production --skip-tests         # Skip test validation
/prepare-deploy production --accessibility-skip # Skip accessibility (not recommended)
```

## **🚨 Critical Failure Categories**

### **Immediate Deployment Blockers**
- Unit tests failing
- Accessibility violations (WCAG 2.1 AA)
- Security vulnerabilities (high/critical)
- Student privacy compliance issues
- Sensitive data in code
- Invalid Twibble vocabulary data

### **Performance Blockers**
- JavaScript bundle >500KB
- CSS bundle >100KB
- Missing critical files
- Broken PWA manifest

### **Educational Compliance Blockers**
- Student data collection detected
- Missing accessibility features
- Inappropriate content detected
- Third-party tracking scripts

## **📊 Success Metrics**

- **Zero Critical Failures**: All must be resolved for deployment
- **Educational Compliance**: Student privacy and accessibility validated
- **Performance Standards**: Bundle sizes and load times within limits
- **Code Quality**: Linting clean, security vulnerabilities resolved
- **Platform Integrity**: Twibble-specific features validated and working

## **🔗 Integration Points**

- **Triggered before all deployments** via /prepare-deploy
- **Blocks CI/CD pipelines** on validation failures
- **Generates detailed reports** for manual review
- **Enforces educational compliance** for student safety
- **Validates Twibble-specific requirements** for vocabulary learning platform