---
name: after-agent-run
description: Automatic documentation synchronization and consistency checks that execute after any Claude Code agent completes its task
trigger: post_agent_execution
---

# After Agent Run Hook

## **🎯 Purpose**
Automatically synchronize documentation, validate consistency, and maintain project integrity after any Claude Code agent completes its work on the Twibble vocabulary learning platform.

## **🔄 Hook Execution Flow**

### **Step 1: Agent Context Analysis**
```bash
# Capture agent execution context
agent_name="$CLAUDE_AGENT_NAME"
agent_files_changed="$CLAUDE_FILES_CHANGED"
execution_time="$(date -u +%Y-%m-%dT%H:%M:%SZ)"
working_directory="$(pwd)"

echo "🤖 Post-agent cleanup for: $agent_name"
echo "📁 Working directory: $working_directory"
echo "📝 Files changed: $(echo "$agent_files_changed" | wc -w)"

# Log agent execution for audit trail
cat >> .claude-agent-log << EOF
$(date -u +%Y-%m-%dT%H:%M:%SZ) | $agent_name | $(echo "$agent_files_changed" | wc -w) files | $(git rev-parse --short HEAD)
EOF
```

**Expected Output:**
```
🤖 Post-agent cleanup for: code-reviewer
📁 Working directory: /Users/USER/Desktop/Twibble
📝 Files changed: 3
```

### **Step 2: Documentation Synchronization**
```bash
echo "📚 Synchronizing documentation..."

# Check if code changes require documentation updates
needs_doc_update=false

# JavaScript/TypeScript changes may require API docs update
if echo "$agent_files_changed" | grep -E '\.(js|ts)$'; then
  echo "🔍 JavaScript changes detected - checking API documentation"
  
  # Extract function signatures that may have changed
  for js_file in $(echo "$agent_files_changed" | grep -E '\.(js|ts)$'); do
    if [ -f "$js_file" ]; then
      # Look for new or modified function declarations
      new_functions=$(grep -E '^(function|const.*=.*=>|class)' "$js_file" | head -5)
      if [ -n "$new_functions" ]; then
        echo "📝 New functions detected in $js_file"
        needs_doc_update=true
      fi
    fi
  done
fi

# HTML changes may require user guide updates
if echo "$agent_files_changed" | grep -E '\.(html)$'; then
  echo "🔍 HTML changes detected - checking user documentation"
  needs_doc_update=true
fi

# CSS changes may require design system updates
if echo "$agent_files_changed" | grep -E '\.(css)$'; then
  echo "🔍 CSS changes detected - checking design system documentation"
  
  # Check if new CSS custom properties were added
  if [ -f "styles.css" ] && grep -E '^[[:space:]]*--[a-zA-Z-]+:' styles.css; then
    echo "🎨 New CSS custom properties detected"
    needs_doc_update=true
  fi
fi

# Twibble-specific files require special handling
if echo "$agent_files_changed" | grep -E '(consolidated_words\.json|phonics_chunks\.txt)'; then
  echo "🎓 Twibble vocabulary data updated"
  needs_doc_update=true
fi

if [ "$needs_doc_update" = true ]; then
  echo "📝 Documentation update required"
  
  # Update README with current stats
  if [ -f "README.md" ]; then
    # Update vocabulary word count if consolidated_words.json changed
    if [ -f "consolidated_words.json" ]; then
      word_count=$(node -e "
        try {
          const words = require('./consolidated_words.json');
          console.log(words.words?.length || 0);
        } catch {
          console.log(0);
        }
      " 2>/dev/null)
      
      # Update word count in README
      sed -i.bak "s/[0-9]* vocabulary words/$word_count vocabulary words/g" README.md
      echo "✅ README updated with current word count: $word_count"
    fi
    
    # Update last modified date
    current_date=$(date '+%Y-%m-%d')
    sed -i.bak "s/Last updated: [0-9-]*/Last updated: $current_date/g" README.md
  fi
  
  # Update FUNCTIONAL_SPEC.md if needed
  if [ -f "FUNCTIONAL_SPEC.md" ] && echo "$agent_files_changed" | grep -E '\.(html|js)$'; then
    echo "🔄 FUNCTIONAL_SPEC.md may need updates - adding note"
    
    # Add a comment about recent changes
    if ! grep -q "<!-- Auto-updated" FUNCTIONAL_SPEC.md; then
      echo "" >> FUNCTIONAL_SPEC.md
      echo "<!-- Auto-updated: $(date -u +%Y-%m-%dT%H:%M:%SZ) after $agent_name execution -->" >> FUNCTIONAL_SPEC.md
    fi
  fi
  
else
  echo "ℹ️  No documentation updates needed"
fi
```

**Expected Output:**
```
📚 Synchronizing documentation...
🔍 JavaScript changes detected - checking API documentation
📝 New functions detected in script.js
📝 Documentation update required
✅ README updated with current word count: 1247
🔄 FUNCTIONAL_SPEC.md may need updates - adding note
```

### **Step 3: Code Consistency Validation**
```bash
echo "🔍 Validating code consistency..."

consistency_issues=()

# Check for consistent file naming
if ls *.html >/dev/null 2>&1; then
  inconsistent_names=$(ls *.html | grep -v -E '^[a-z]+(-[a-z]+)*\.html$' || true)
  if [ -n "$inconsistent_names" ]; then
    consistency_issues+=("HTML files with inconsistent naming: $inconsistent_names")
  fi
fi

# Validate Twibble-specific requirements
if [ -f "script.js" ]; then
  # Check for required Twibble functions
  required_functions=("createLesson" "startActivity" "saveProgress")
  missing_functions=()
  
  for func in "${required_functions[@]}"; do
    if ! grep -q "$func" script.js; then
      missing_functions+=("$func")
    fi
  done
  
  if [ ${#missing_functions[@]} -gt 0 ]; then
    consistency_issues+=("Missing required functions: ${missing_functions[*]}")
  fi
fi

# Check for design system compliance in CSS
if [ -f "styles.css" ]; then
  # Verify CSS custom properties are used instead of hardcoded values
  hardcoded_colors=$(grep -E '#[0-9A-Fa-f]{6}|rgb\(|rgba\(' styles.css | wc -l)
  if [ "$hardcoded_colors" -gt 10 ]; then  # Allow some hardcoded values
    consistency_issues+=("Excessive hardcoded colors in CSS: $hardcoded_colors instances")
  fi
  
  # Check that required design tokens exist
  required_tokens=("--color-primary" "--font-size-base" "--space-4")
  missing_tokens=()
  
  for token in "${required_tokens[@]}"; do
    if ! grep -q "$token" styles.css; then
      missing_tokens+=("$token")
    fi
  done
  
  if [ ${#missing_tokens[@]} -gt 0 ]; then
    consistency_issues+=("Missing design tokens: ${missing_tokens[*]}")
  fi
fi

# Report consistency issues
if [ ${#consistency_issues[@]} -gt 0 ]; then
  echo "⚠️  Consistency issues found:"
  for issue in "${consistency_issues[@]}"; do
    echo "  - $issue"
  done
  
  # Create consistency report
  cat > .consistency-report.md << EOF
# Consistency Issues Report

Generated: $execution_time
Agent: $agent_name

## Issues Found:
$(for issue in "${consistency_issues[@]}"; do echo "- $issue"; done)

## Recommended Actions:
- Review code changes for design system compliance
- Ensure all required Twibble functions are present
- Follow file naming conventions
- Use CSS custom properties instead of hardcoded values

EOF
  
  echo "📝 Consistency report saved to .consistency-report.md"
else
  echo "✅ No consistency issues found"
fi
```

**Expected Output:**
```
🔍 Validating code consistency...
✅ HTML file naming consistent
✅ All required Twibble functions present
⚠️  Consistency issues found:
  - Excessive hardcoded colors in CSS: 15 instances
📝 Consistency report saved to .consistency-report.md
```

### **Step 4: Asset Integrity Check**
```bash
echo "🖼️  Checking asset integrity..."

asset_issues=()

# Verify Twibble vocabulary data integrity
if [ -f "consolidated_words.json" ]; then
  # Validate JSON structure
  if ! node -e "JSON.parse(require('fs').readFileSync('consolidated_words.json', 'utf8'))" 2>/dev/null; then
    asset_issues+=("consolidated_words.json: Invalid JSON structure")
  else
    # Check data structure
    validation_result=$(node -e "
      try {
        const data = require('./consolidated_words.json');
        if (!data.words || !Array.isArray(data.words)) {
          console.log('Missing or invalid words array');
        } else if (data.words.length < 100) {
          console.log('Low word count: ' + data.words.length);
        } else {
          // Check sample word structure
          const sample = data.words[0];
          if (!sample.text || !sample.imageUrl) {
            console.log('Invalid word structure');
          } else {
            console.log('Valid structure, ' + data.words.length + ' words');
          }
        }
      } catch (e) {
        console.log('Error: ' + e.message);
      }
    " 2>/dev/null)
    
    if echo "$validation_result" | grep -q "Error\|Missing\|Invalid\|Low"; then
      asset_issues+=("consolidated_words.json: $validation_result")
    else
      echo "✅ Vocabulary data valid: $validation_result"
    fi
  fi
fi

# Check phonics chunks file
if [ -f "phonics_chunks.txt" ]; then
  # Validate format (word|chunk1,chunk2,chunk3)
  invalid_lines=$(grep -v -E '^[a-zA-Z]+\|[a-zA-Z,]+$' phonics_chunks.txt | wc -l)
  if [ "$invalid_lines" -gt 0 ]; then
    asset_issues+=("phonics_chunks.txt: $invalid_lines invalid format lines")
  else
    chunk_count=$(wc -l < phonics_chunks.txt)
    echo "✅ Phonics chunks valid: $chunk_count entries"
  fi
fi

# Check PWA manifest
if [ -f "manifest.json" ]; then
  if ! node -e "JSON.parse(require('fs').readFileSync('manifest.json', 'utf8'))" 2>/dev/null; then
    asset_issues+=("manifest.json: Invalid JSON structure")
  else
    # Check required PWA fields
    manifest_check=$(node -e "
      const manifest = require('./manifest.json');
      const required = ['name', 'short_name', 'start_url', 'display', 'icons'];
      const missing = required.filter(field => !manifest[field]);
      if (missing.length > 0) {
        console.log('Missing fields: ' + missing.join(', '));
      } else {
        console.log('Valid PWA manifest');
      }
    " 2>/dev/null)
    
    if echo "$manifest_check" | grep -q "Missing"; then
      asset_issues+=("manifest.json: $manifest_check")
    else
      echo "✅ $manifest_check"
    fi
  fi
fi

# Report asset issues
if [ ${#asset_issues[@]} -gt 0 ]; then
  echo "⚠️  Asset integrity issues:"
  for issue in "${asset_issues[@]}"; do
    echo "  - $issue"
  done
  
  # Append to consistency report if it exists
  if [ -f ".consistency-report.md" ]; then
    echo "" >> .consistency-report.md
    echo "## Asset Integrity Issues:" >> .consistency-report.md
    for issue in "${asset_issues[@]}"; do
      echo "- $issue" >> .consistency-report.md
    done
  fi
else
  echo "✅ All assets have integrity"
fi
```

**Expected Output:**
```
🖼️  Checking asset integrity...
✅ Vocabulary data valid: Valid structure, 1247 words
✅ Phonics chunks valid: 892 entries
✅ Valid PWA manifest
✅ All assets have integrity
```

### **Step 5: Performance Impact Assessment**
```bash
echo "⚡ Assessing performance impact..."

# Check if performance-critical files were modified
perf_critical_files=("script.js" "styles.css" "*.html")
perf_impact=false

for pattern in "${perf_critical_files[@]}"; do
  if echo "$agent_files_changed" | grep -E "$pattern" >/dev/null; then
    perf_impact=true
    break
  fi
done

if [ "$perf_impact" = true ]; then
  echo "🔍 Performance-critical files modified - checking bundle sizes"
  
  # Check current bundle sizes
  if [ -f "script.js" ]; then
    js_size=$(stat --format=%s script.js 2>/dev/null | awk '{print int($1/1024)}' || echo "unknown")
    echo "📊 JavaScript size: ${js_size}KB"
    
    if [ "$js_size" != "unknown" ] && [ "$js_size" -gt 500 ]; then
      echo "⚠️  JavaScript bundle exceeds 500KB performance budget"
    fi
  fi
  
  if [ -f "styles.css" ]; then
    css_size=$(stat --format=%s styles.css 2>/dev/null | awk '{print int($1/1024)}' || echo "unknown")
    echo "📊 CSS size: ${css_size}KB"
    
    if [ "$css_size" != "unknown" ] && [ "$css_size" -gt 100 ]; then
      echo "⚠️  CSS bundle exceeds 100KB performance budget"
    fi
  fi
  
  # Check for performance-impacting patterns
  perf_warnings=()
  
  if [ -f "script.js" ]; then
    # Check for console.log statements (development code)
    console_count=$(grep -c "console\." script.js || echo "0")
    if [ "$console_count" -gt 5 ]; then
      perf_warnings+=("Excessive console statements: $console_count")
    fi
    
    # Check for synchronous operations
    sync_operations=$(grep -c -E "(\.sync\(|synchronous)" script.js || echo "0")
    if [ "$sync_operations" -gt 0 ]; then
      perf_warnings+=("Potential synchronous operations: $sync_operations")
    fi
  fi
  
  if [ ${#perf_warnings[@]} -gt 0 ]; then
    echo "⚠️  Performance warnings:"
    for warning in "${perf_warnings[@]}"; do
      echo "  - $warning"
    done
  fi
  
else
  echo "ℹ️  No performance-critical files modified"
fi
```

**Expected Output:**
```
⚡ Assessing performance impact...
🔍 Performance-critical files modified - checking bundle sizes
📊 JavaScript size: 487KB
📊 CSS size: 89KB
✅ Bundle sizes within performance budgets
```

### **Step 6: Git Status and Cleanup**
```bash
echo "🔄 Git status and cleanup..."

# Check if any files were modified by the hook
modified_by_hook=$(git status --porcelain | wc -l)

if [ "$modified_by_hook" -gt 0 ]; then
  echo "📝 Hook modified $modified_by_hook files:"
  git status --porcelain
  
  # Auto-commit documentation updates if appropriate
  if git status --porcelain | grep -E '(README\.md|FUNCTIONAL_SPEC\.md|\.md)$'; then
    echo "📚 Auto-committing documentation updates"
    
    git add README.md FUNCTIONAL_SPEC.md *.md 2>/dev/null || true
    git commit -m "docs: auto-update after $agent_name execution

- Updated documentation consistency
- Refreshed statistics and references  
- Synchronized with code changes

🤖 Auto-committed by after-agent-run hook" || echo "⚠️  Auto-commit failed"
  fi
else
  echo "✅ No files modified by hook"
fi

# Clean up temporary files
cleanup_files=(".consistency-report.md.bak" "README.md.bak" "lighthouse-*.json")
for cleanup_file in "${cleanup_files[@]}"; do
  rm -f "$cleanup_file" 2>/dev/null || true
done

echo "🧹 Temporary files cleaned up"
```

**Expected Output:**
```
🔄 Git status and cleanup...
📝 Hook modified 2 files:
M README.md
M FUNCTIONAL_SPEC.md
📚 Auto-committing documentation updates
[main 1a2b3c4] docs: auto-update after code-reviewer execution
 2 files changed, 3 insertions(+), 2 deletions(-)
🧹 Temporary files cleaned up
```

### **Step 7: Hook Summary and Notifications**
```bash
echo ""
echo "🎉 After-Agent-Run Hook Complete!"
echo "=================================="
echo "🤖 Agent: $agent_name"
echo "📅 Execution: $execution_time"
echo "📝 Files Changed: $(echo "$agent_files_changed" | wc -w)"
echo "📚 Docs Updated: $([ "$needs_doc_update" = true ] && echo "Yes" || echo "No")"
echo "⚠️  Issues Found: ${#consistency_issues[@]} consistency, ${#asset_issues[@]} asset"
echo "⚡ Performance: $([ "$perf_impact" = true ] && echo "Checked" || echo "No impact")"
echo ""

# Create hook execution log entry
hook_log_entry="$(date -u +%Y-%m-%dT%H:%M:%SZ) | after-agent-run | $agent_name | $(echo "$agent_files_changed" | wc -w) files | $([ "$needs_doc_update" = true ] && echo "docs-updated" || echo "no-docs") | ${#consistency_issues[@]} issues"

echo "$hook_log_entry" >> .claude-hook-log

# Summary for agent integration
if [ ${#consistency_issues[@]} -gt 0 ] || [ ${#asset_issues[@]} -gt 0 ]; then
  echo "⚠️  Issues detected - review .consistency-report.md"
else
  echo "✅ All checks passed - project integrity maintained"
fi

echo ""
echo "📋 Next Actions:"
if [ "$needs_doc_update" = true ]; then
  echo "- Review auto-updated documentation"
fi
if [ ${#consistency_issues[@]} -gt 0 ]; then
  echo "- Address consistency issues in .consistency-report.md"
fi
if [ "$perf_impact" = true ]; then
  echo "- Consider running /compact to optimize bundle"
fi
echo "- Continue development or run /checkpoint for full validation"
```

**Expected Output:**
```
🎉 After-Agent-Run Hook Complete!
==================================
🤖 Agent: code-reviewer
📅 Execution: 2024-01-15T19:45:12Z
📝 Files Changed: 3
📚 Docs Updated: Yes
⚠️  Issues Found: 1 consistency, 0 asset
⚡ Performance: Checked

⚠️  Issues detected - review .consistency-report.md

📋 Next Actions:
- Review auto-updated documentation
- Address consistency issues in .consistency-report.md
- Consider running /compact to optimize bundle
- Continue development or run /checkpoint for full validation
```

## **🔧 Configuration Options**

### **Hook Configuration Variables**
```bash
# Set in .claude/settings.local.json or environment
CLAUDE_AUTO_COMMIT_DOCS=true          # Auto-commit documentation updates
CLAUDE_SKIP_CONSISTENCY_CHECK=false   # Skip consistency validation
CLAUDE_PERFORMANCE_CHECK=true         # Check performance impact
CLAUDE_ASSET_VALIDATION=true          # Validate asset integrity
CLAUDE_LOG_AGENT_RUNS=true            # Log all agent executions
```

### **Agent-Specific Behavior**
```bash
# Different behavior based on agent type
case "$agent_name" in
  "code-reviewer")
    # Extra validation after code review
    run_extra_linting=true
    ;;
  "docs-generator")
    # Skip doc sync after docs agent
    needs_doc_update=false
    ;;
  "ux-a11y-auditor")
    # Extra accessibility validation
    run_a11y_check=true
    ;;
esac
```

## **🚨 Error Handling**

### **Hook Failure Recovery**
```bash
# If hook fails, don't break agent workflow
trap 'echo "⚠️  Hook failed but agent execution preserved"; exit 0' ERR

# Critical failure scenarios
if [ ! -f "consolidated_words.json" ]; then
  echo "🚨 Critical: Twibble vocabulary data missing!"
  echo "This may break the vocabulary learning functionality"
  # Don't exit - let user decide
fi
```

### **Git Operation Failures**
```bash
# Handle git failures gracefully
if ! git add . &>/dev/null; then
  echo "⚠️  Git add failed - manual intervention may be needed"
fi

if ! git commit -m "Auto-update" &>/dev/null; then
  echo "ℹ️  No changes to commit or commit failed"
fi
```

## **📊 Success Metrics**

- **Documentation Sync**: Automatic updates to README, specs when needed
- **Consistency Validation**: Code style and requirements compliance checked
- **Asset Integrity**: Twibble vocabulary and PWA data validated
- **Performance Monitoring**: Bundle size and optimization warnings
- **Git Integration**: Clean commits and status management
- **Error Prevention**: Issues caught before they impact users

## **🔗 Integration Points**

- **Triggered by all Claude Code agents** automatically
- **Integrates with /checkpoint** command for validation
- **Feeds into /generate-prp** for comprehensive PR analysis  
- **Compatible with CI/CD pipelines** via git hooks
- **Supports Twibble-specific validations** for vocabulary learning platform