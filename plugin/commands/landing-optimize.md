# Blog Quality Optimization (ACTION)

Validate article quality with automated checks for frontmatter, markdown formatting, and spec compliance.

**100% ACTION**: This command validates AND automatically fixes detected issues, not just reports them.

## Usage

### Single Article (Recommended)

```bash
/blog-optimize "lang/article-slug"
```

**Examples**:
```bash
/blog-optimize "en/nodejs-tracing"
/blog-optimize "fr/microservices-logging"
```

**Token usage**: ~10k-15k tokens per article
**Outputs**:
- `articles/[topic].md` (auto-corrected version) ✅ **ACTIONABLE**
- `.specify/quality/[topic]-fixes.md` (changelog)

### Global Validation (️  High Token Usage)

```bash
/blog-optimize
```

**️  WARNING**: This will validate ALL articles in your content directory.

**Token usage**: 50k-500k+ tokens (depending on article count)
**Cost**: Can be expensive (e.g., 50 articles = ~500k tokens)
**Duration**: 20-60 minutes for large blogs
**Use case**: Initial audit, bulk validation, CI/CD pipelines

**Recommendation**: Validate articles individually unless you need a full audit.

## Prerequisites

 **Required**: Article must exist at `articles/[topic].md`

If article doesn't exist, run `/blog-generate` or `/blog-marketing` first.

## What This Command Does

Delegates to the **quality-optimizer** subagent to validate AND auto-fix article quality:

**Validation Phases**:
- **Spec Compliance**: Validates against `.spec/blog.spec.json` requirements
- **Frontmatter Structure**: Checks required fields and format
- **Markdown Quality**: Validates syntax, headings, links, code blocks
- **SEO Elements**: Checks meta description, keywords, internal links
- **Readability**: Analyzes sentence length, paragraph size, passive voice
- **Brand Voice**: Validates against `voice_dont` anti-patterns

**Auto-Fix Phase** (NEW):
- **Automatically corrects detected issues** with backup creation
- Fixes missing metadata (postType, funnelStage, readingTime)
- Corrects structure issues (missing H1, FAQ section)
- Enforces component compliance (comparison tables, etc.)

**Time**: 10-15 minutes
**Outputs**:
- `articles/[topic].md` (corrected version) ✅ **ACTIONABLE**
- `.specify/quality/[topic]-fixes.md` (what was changed)

## Instructions

Create a new subagent conversation with the `quality-optimizer` agent.

**Provide the following prompt**:

```
You are validating the quality of a blog article.

**Article Path**: articles/$ARGUMENTS.md

Follow your Five-Phase Process:

1. **Spec Compliance Validation** (5-7 min):
   - Generate validation script in /tmp/validate-spec-$$.sh
   - Load .spec/blog.spec.json (if exists)
   - Check frontmatter required fields
   - Validate review_rules compliance (must_have, must_avoid)
   - Check brand voice anti-patterns (voice_dont)
   - Run script and capture results

2. **Markdown Quality Validation** (5-10 min):
   - Generate validation script in /tmp/validate-markdown-$$.sh
   - Check heading hierarchy (one H1, proper nesting)
   - Validate link syntax (no broken links)
   - Check code blocks (properly closed, language tags)
   - Verify images have alt text
   - Run script and capture results

3. **SEO and Performance Validation** (3-5 min):
   - Generate validation script in /tmp/validate-seo-$$.sh
   - Check meta description length (150-160 chars)
   - Validate keyword presence in critical locations
   - Count internal links (minimum 3 recommended)
   - Calculate readability metrics
   - Run script and capture results

4. **Post Type & Component Validation** (3-5 min):
   - Check postType compliance (actionnable/aspirationnel/analytique/anthropologique)
   - Validate funnelStage (TOFU/MOFU/BOFU)
   - Check required components per post type
   - Verify component structure

5. **Auto-Fix** (10-15 min) ✅ NEW - ACTION PHASE:
   - **CRITICAL**: Create backup before modifying
   - Auto-fix missing metadata (postType, funnelStage, readingTime)
   - Fix structure issues (missing H1, FAQ section if BOFU/MOFU)
   - Correct component compliance (add comparison tables, etc.)
   - Generate fix changelog
   - Save corrected article

**Output Locations**:
- Corrected article: `articles/$ARGUMENTS.md` ✅ **ACTIONABLE**
- Fix changelog: `.specify/quality/$ARGUMENTS-fixes.md`
- Backup: `articles/$ARGUMENTS/.backup/` (timestamped)

**Important**:
- **ALWAYS create backup before modifying** (articles/.backup/)
- All validation scripts in /tmp/
- Provide rollback instructions in changelog
- Include before/after metrics

Begin validation and auto-fix now.
```

## Expected Outputs

After completion, verify that **TWO files** exist:

### 1. Corrected Article (✅ **ACTIONABLE** - `articles/[topic].md`)

The article has been automatically corrected with:
- Missing metadata added (postType, funnelStage, readingTime)
- Structure issues fixed (H1, FAQ section if applicable)
- Component compliance enforced (comparison tables, etc.)
- **Backup created** in `articles/[topic]/.backup/[timestamp].md`

### 2. Fix Changelog (`.specify/quality/[topic]-fixes.md`)

Comprehensive report containing:

 **Auto-Fixes Applied**:
- List of all automatic corrections made
- Before/after comparison for each fix

 **Validation Results**:
- Passed checks section
- Remaining warnings (non-critical)
- Any unfixable issues

 **Metrics Dashboard**:
- Frontmatter completeness (before/after)
- Content structure statistics
- SEO metrics
- Readability scores

 **Rollback Instructions**:
- How to restore from backup if needed
- Location of backup file

 **Validation Scripts**:
- List of generated scripts in /tmp/
- Instructions for manual review/cleanup

## Interpreting Results

###  All Checks Passed (No Auto-Fixes Needed)

```
 Passed Checks (12/12)
 Auto-Fixes: None needed

No issues found! Article is ready to publish.
```

**Next Steps**: Review the article one final time and publish.

### ️  Auto-Fixes Applied

```
 Auto-Fixes Applied (3)

Fixed:
- Added missing postType: "actionnable" (detected from content)
- Added funnelStage: "MOFU" (detected from keywords)
- Added readingTime: "8 min" (calculated from word count)

 Passed Checks (12/12) - After fixes

Article has been corrected and is ready to publish!
```

**Next Steps**:
- Review corrected article in `articles/[topic].md`
- Check `.specify/quality/[topic]-fixes.md` for details
- Publish if satisfied, or rollback from backup if needed

###  Critical Issues (Unfixable)

```
 Auto-Fixes Applied (2)
 Critical Issues Remaining (1)

Auto-fixed:
- Added missing metadata

Could not auto-fix:
- Broken external link to https://example.com/404

Manual intervention required.
```

**Next Steps**:
- Fix unfixable issues manually
- Re-run `/blog-optimize` to verify

## Review Checklist

Before considering article complete:

1. **Frontmatter Complete**?
   - All required fields present
   - Meta description 150-160 chars
   - Valid date format

2. **Content Quality**?
   - Proper heading hierarchy
   - No broken links
   - All images have alt text
   - Code blocks properly formatted

3. **SEO Optimized**?
   - Keyword in title and headings
   - 3+ internal links
   - Meta description compelling
   - Readable paragraphs (<150 words)

4. **Spec Compliant**?
   - Meets all `must_have` requirements
   - Avoids all `must_avoid` patterns
   - Follows brand voice guidelines

## Next Steps

After validation is complete:

### If All Checks Pass 

```bash
# Publish the article
# (copy to your CMS or commit to git)
```

### If Issues Found ️ 

```bash
# Fix issues manually or use other commands

# If content needs rewriting:
/blog-marketing "topic-name"  # Regenerate with fixes

# If SEO needs adjustment:
/blog-seo "topic-name"  # Regenerate SEO brief

# After fixes, re-validate:
/blog-optimize "topic-name"
```

## When to Use This Command

Use `/blog-optimize` when you need to:

-  **Before publishing**: Final quality check
-  **After manual edits**: Validate changes didn't break anything
-  **Updating old articles**: Check compliance with current standards
-  **Troubleshooting**: Identify specific issues in article
-  **Learning**: See what makes a quality article

**For full workflow**: `/blog-generate` includes optimization as final step (optional).

## Tips

1. **Run after each major edit**: Catch issues early
2. **Review validation scripts**: Learn what good quality means
3. **Keep constitution updated**: Validation reflects your current standards
4. **Fix critical first**: Warnings can wait, critical issues block publishing
5. **Use metrics to improve**: Track quality trends over time

## Requesting Re-validation

After fixing issues:

```bash
/blog-optimize "topic-name"
```

The agent will re-run all checks and show improvements:

```
Previous:  3 critical, ️  2 warnings
Current:   All checks passed!

Improvements:
- Fixed missing frontmatter field 
- Added alt text to all images 
- Closed unclosed code block 
```

## Validation Script Cleanup

Scripts are generated in `/tmp/` and can be manually removed:

```bash
# List validation scripts
ls /tmp/validate-*.sh

# Remove all validation scripts
rm /tmp/validate-*.sh

# Or let OS auto-cleanup on reboot
```

## Error Handling

If validation fails:
- **Check article exists**: Verify path `articles/[topic].md`
- **Check constitution valid**: Run `bash scripts/validate-constitution.sh`
- **Review script output**: Check `/tmp/validate-*.sh` for errors
- **Try with simpler article**: Test validation on known-good article

---

**Ready to validate?** Provide the topic name and execute this command.
