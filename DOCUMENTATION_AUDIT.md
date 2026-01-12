# Jmix AI Documentation Audit Report

## Executive Summary

This audit identifies **critical contradictions**, **missing information**, and **potential AI agent pitfalls** in the Jmix project documentation. The documentation is generally well-structured but has several inconsistencies that could confuse AI agents and developers.

---

## 1. CONTRADICTIONS BETWEEN SKILLS AND RULES

### 🔴 CRITICAL: Field Injection (@Autowired) Contradiction

**Location:** `.cursorrules`, `003-services-rule.mdc`, `03-services-skill.md`

**Contradiction:**
- `.cursorrules` (line 38): "Field `@Autowired`" is **FORBIDDEN**
- `003-services-rule.mdc` (line 52): "Field `@Autowired` (use constructor injection)" is **FORBIDDEN**
- `03-services-skill.md` (lines 35-47): Shows **"Allowed: Field Injection (CUBA Style)"** with example

**Impact:** High - AI agents will be confused about whether field injection is allowed in services.

**Recommendation:** 
- **Option A (Preferred):** Remove field injection example from skills, keep it forbidden everywhere
- **Option B:** Update rules to explicitly allow field injection in services but recommend constructor injection, and clarify that `.cursorrules` applies to entities/views only

---

### 🟡 MODERATE: @Autowired in Views

**Location:** `02-views-skill.md`, `002-views-rule.mdc`, `.cursorrules`

**Contradiction:**
- `02-views-skill.md` (lines 159-174): Shows `@Autowired` for Spring beans in views as **standard practice**
- `.cursorrules` (line 38): "Field `@Autowired`" is **FORBIDDEN** (no exception for views)
- `002-views-rule.mdc`: Doesn't mention `@Autowired` at all

**Impact:** Medium - Views skill shows field injection but general rules forbid it.

**Recommendation:** Clarify that `@Autowired` is **allowed for Spring beans in views** (as shown in skills), but `@ViewComponent` is used for UI components. Update `.cursorrules` to specify: "Field `@Autowired` forbidden in entities/services, allowed in views for Spring beans."

---

### 🟡 MODERATE: Lombok Recommendation Level

**Location:** `000-jmix-overview-rule.mdc`, `001-entities-rule.mdc`, `007-dto-rule.mdc`

**Contradiction:**
- `000-jmix-overview-rule.mdc` (line 37): "**Lombok** is NOT recommended" (general)
- `001-entities-rule.mdc` (line 45): "Lombok (`@Data`, `@Getter`, etc.) - any annotations forbidden" (entities)
- `007-dto-rule.mdc` (line 79): "**Allowed on DTOs** (unlike entities)" (DTOs)

**Impact:** Low - The distinction is clear (forbidden on entities, allowed on DTOs), but "NOT recommended" in general rule is confusing.

**Recommendation:** Update general rule to: "Lombok forbidden on entities, allowed on DTOs (though Records preferred for immutable DTOs)."

---

### 🟡 MODERATE: EntityManager Usage

**Location:** `03-services-skill.md`, `003-services-rule.mdc`, `.cursorrules`

**Contradiction:**
- `.cursorrules` (line 39): "EntityManager" is **FORBIDDEN** (absolute)
- `003-services-rule.mdc` (line 53): "EntityManager without `@Transactional`" is forbidden (conditional)
- `03-services-skill.md` (lines 88-129): Shows **legitimate EntityManager usage** for bulk operations/native SQL

**Impact:** Medium - General rule says forbidden, but skills show valid use cases.

**Recommendation:** Update `.cursorrules` to: "EntityManager forbidden for regular CRUD (use DataManager). Allowed only for bulk operations/native SQL with `@Transactional`."

---

### 🟢 MINOR: Testing Mock Annotation

**Location:** `05-testing-skill.md`, `005-testing-rule.mdc`

**Contradiction:**
- `05-testing-skill.md` (line 36): Mentions `@MockBean` is deprecated, use `@MockitoBean`
- `005-testing-rule.mdc` (line 50): Says `@MockBean` is deprecated, use `@MockitoBean`

**Status:** ✅ Consistent - Both correctly state the deprecation.

---

## 2. MISSING CRITICAL INFORMATION

### 🔴 CRITICAL: DTO Name Prefix Requirement

**Location:** `007-dto-rule.mdc`, `07-dto-skill.md`

**Missing:** 
- Rules show `@JmixEntity(name = "app_CustomerSummaryDto")` with prefix
- Skills show `@JmixEntity(name = "CustomerSummaryDto")` **without prefix** (line 34)
- No clear explanation that DTO names MUST have prefix to avoid collisions

**Impact:** High - AI agents may create DTOs without prefixes, causing bean name collisions.

**Recommendation:** 
- Add explicit rule: "DTO entity names MUST use prefix (e.g., `app_`, `mod_`) to avoid collisions"
- Update skills example to show prefix consistently
- Add to checklist: "DTO name has prefix"

---

### 🔴 CRITICAL: View Dependency Injection Clarification

**Location:** `002-views-rule.mdc`, `02-views-skill.md`

**Missing:**
- Clear explanation of when to use `@Autowired` vs `@ViewComponent`
- No mention that `@Autowired` is acceptable for Spring beans in views

**Impact:** High - AI agents may incorrectly use `@ViewComponent` for services or `@Autowired` for UI components.

**Recommendation:** Add section to rules:
```markdown
## Dependency Injection in Views
- `@Autowired`: Use for Spring beans (services, DataManager, etc.)
- `@ViewComponent`: Use for UI components defined in XML (DataGrid, TextField, etc.)
- Constructor injection: Not standard for views (use field injection)
```

---

### 🟡 MODERATE: EntityManager Proper Usage Pattern

**Location:** `003-services-rule.mdc`

**Missing:**
- When EntityManager is acceptable (bulk operations, native SQL)
- That it requires `@PersistenceContext` injection
- That it bypasses Jmix security/row-level security

**Impact:** Medium - Developers/AI won't know when EntityManager is appropriate.

**Recommendation:** Add section:
```markdown
## EntityManager (Advanced)
Use ONLY for:
- Bulk updates/deletes (JPQL)
- Native SQL queries
- Stored procedures

Rules:
- Must use `@PersistenceContext` (not `@Autowired`)
- Must be inside `@Transactional`
- Does NOT apply Jmix row-level security
- See `03-services-skill.md` for examples
```

---

### 🟡 MODERATE: Fetch Plan Default Strategy

**Location:** `009-fetchplans-rule.mdc`, `09-fetchplans-skill.md`

**Missing:**
- Clear guidance on what fetch plan to use when none specified
- Default behavior if fetch plan omitted in DataManager calls

**Impact:** Medium - AI agents may omit fetch plans and cause N+1 issues.

**Recommendation:** Add to rules:
```markdown
## Default Behavior
- If no fetch plan specified: loads `_base` by default
- Always specify fetch plan explicitly for clarity
- List views: prefer `_instance_name` for references
```

---

### 🟡 MODERATE: Testing Authentication Pattern

**Location:** `005-testing-rule.mdc`

**Missing:**
- Complete example of `SystemAuthenticator` usage
- That `@WithUserDetails` doesn't work with Jmix security context

**Impact:** Medium - AI agents may use wrong authentication method.

**Recommendation:** Add example:
```java
@BeforeEach
void setUp() {
    systemAuthenticator.begin("admin");
}

@AfterEach
void tearDown() {
    systemAuthenticator.end();
}
```

---

### 🟢 MINOR: EnumUtils Alternative Not in Rules

**Location:** `008-enums-rule.mdc`, `08-enums-skill.md`

**Missing:**
- Rules don't mention `EnumUtils.fromId()` alternative
- Skills show it as preferred approach

**Impact:** Low - Rules show manual `fromId()`, skills show utility.

**Recommendation:** Add to rules:
```markdown
## Alternative: EnumUtils
Prefer `EnumUtils.fromId(OrderStatus.class, storedValue)` over manual `fromId()`:
- Shorter code
- Handles null safely
- Consistent across codebase
```

---

### 🟢 MINOR: Liquibase UUID Type Variable

**Location:** `006-liquibase-rule.mdc`, `06-liquibase-skill.md`

**Missing:**
- Rules mention `${uuid.type}` but don't explain what it resolves to
- No mention of database-specific behavior

**Impact:** Low - Developers may not understand the variable.

**Recommendation:** Add note:
```markdown
## UUID Type Variable
`${uuid.type}` resolves to database-specific type:
- PostgreSQL: `uuid`
- HSQLDB: `varchar(36)`
- MySQL: `varchar(36)`
Always use `${uuid.type}` for cross-database compatibility.
```

---

## 3. WHAT WOULD TRIP UP AN AI AGENT

### 🔴 CRITICAL: Bean Name Prefix Inconsistency

**Problem:** 
- Services rules say "MUST use prefix" (`app_`, `mod_`)
- DTO rules show prefix in example but don't emphasize it's required
- Skills show DTO without prefix in one example

**Why it trips up AI:**
- AI sees "MUST" in services but not in DTOs
- May create DTOs without prefix
- May create services without prefix if it misses the rule

**Fix:** Make prefix requirement explicit in all relevant rules with "MUST" language.

---

### 🔴 CRITICAL: View Dependency Injection Ambiguity

**Problem:**
- `.cursorrules` says "Field `@Autowired`" forbidden
- Views skill shows `@Autowired` as standard
- No clear exception documented

**Why it trips up AI:**
- AI follows general rule and avoids `@Autowired` in views
- Creates incorrect code using constructor injection in views
- May use `@ViewComponent` for services

**Fix:** Add explicit exception in `.cursorrules`: "Field `@Autowired` forbidden in entities/services, **allowed in views for Spring beans**."

---

### 🟡 MODERATE: EntityManager Absolute vs Conditional

**Problem:**
- `.cursorrules` says "EntityManager" forbidden (absolute)
- Skills show legitimate usage
- Rules say "without @Transactional" forbidden (conditional)

**Why it trips up AI:**
- AI follows absolute rule and never uses EntityManager
- Misses valid use cases (bulk operations, native SQL)
- Creates inefficient code using DataManager for bulk updates

**Fix:** Update `.cursorrules` to be conditional: "EntityManager forbidden for regular CRUD (use DataManager). Allowed only for bulk operations/native SQL."

---

### 🟡 MODERATE: Fetch Plan Omission

**Problem:**
- Rules say "use `_base` by default"
- But don't explain what happens if omitted
- No explicit requirement to always specify

**Why it trips up AI:**
- AI may omit fetch plans thinking framework handles it
- Creates N+1 query problems
- Doesn't optimize for list vs detail views

**Fix:** Add explicit rule: "Always specify fetch plan explicitly. Default is `_base` but be explicit for clarity."

---

### 🟡 MODERATE: Testing Transaction Annotation

**Problem:**
- Rules say "NO `@Transactional` on tests"
- But don't explain why (Jmix DataManager manages its own)
- Skills mention it but don't emphasize enough

**Why it trips up AI:**
- AI may add `@Transactional` thinking it's needed
- Causes lazy loading issues
- False positive test results

**Fix:** Add prominent warning: "⚠️ NEVER use `@Transactional` on test methods - Jmix DataManager manages transactions internally. This causes lazy loading failures."

---

### 🟢 MINOR: Enum Storage Type Confusion

**Problem:**
- Rules show storing as String/Integer
- But don't emphasize entity field must be String/Integer, not enum type
- Skills show it correctly but rules are less clear

**Why it trips up AI:**
- AI may create `private OrderStatus status;` instead of `private String status;`
- Breaks persistence

**Fix:** Add explicit rule: "Entity field MUST be String/Integer, NOT enum type. Use getter/setter to convert."

---

### 🟢 MINOR: Liquibase Changeset ID Format

**Problem:**
- Rules show two formats: `NNN-entity-N` and `YYYYMMDD-HHMM-topic`
- No guidance on when to use which
- Risk of collision if format inconsistent

**Why it trips up AI:**
- AI may mix formats
- Creates non-unique IDs
- Breaks changelog execution

**Fix:** Add guidance: "Prefer `NNN-entity-N` for entity creation, `YYYYMMDD-HHMM-topic` for migrations/fixes."

---

## 4. IMPROVEMENT SUGGESTIONS

### 🔴 HIGH PRIORITY

1. **Resolve @Autowired Contradiction**
   - Update `.cursorrules` to explicitly allow `@Autowired` in views for Spring beans
   - Remove or clearly mark field injection example in services skill as "legacy/CUBA migration only"
   - Add exception clause to general rule

2. **Clarify DTO Name Prefix Requirement**
   - Add "MUST use prefix" to DTO rules (like services)
   - Update all DTO examples to show prefix
   - Add to checklist

3. **Add View Dependency Injection Section**
   - Create clear section in views rule explaining `@Autowired` vs `@ViewComponent`
   - Add examples showing both patterns
   - Clarify constructor injection is not standard for views

4. **Update EntityManager Rule**
   - Change from absolute "forbidden" to conditional
   - Add "when to use EntityManager" section
   - Reference skills for examples

5. **Add Testing Transaction Warning**
   - Prominent warning box in testing rules
   - Explain why (Jmix DataManager manages transactions)
   - Add to forbidden list with explanation

---

### 🟡 MEDIUM PRIORITY

6. **Standardize Fetch Plan Guidance**
   - Add "always specify explicitly" rule
   - Clarify default behavior
   - Add performance considerations

7. **Add EnumUtils to Rules**
   - Include `EnumUtils.fromId()` as preferred approach
   - Show comparison with manual `fromId()`
   - Update examples

8. **Clarify Liquibase UUID Variable**
   - Explain what `${uuid.type}` resolves to
   - Show database-specific examples
   - Add to column types table

9. **Add Testing Authentication Example**
   - Complete `SystemAuthenticator` example in rules
   - Show JUnit Extension pattern
   - Explain why `@WithUserDetails` doesn't work

10. **Standardize Changeset ID Format**
    - Provide clear guidance on when to use which format
    - Add examples for both patterns
    - Emphasize uniqueness requirement

---

### 🟢 LOW PRIORITY

11. **Add Cross-References**
    - Link between rules and skills
    - "See also" sections
    - Related topics references

12. **Add Common Pitfalls Section**
    - "What would trip up an AI agent" examples
    - Real-world mistakes to avoid
    - Quick reference for edge cases

13. **Standardize Code Examples**
    - Consistent formatting
    - Complete imports
    - Realistic examples (not just snippets)

14. **Add Version Information**
    - Jmix version compatibility notes
    - API changes between versions
    - Deprecation warnings

15. **Improve Checklist Completeness**
    - More detailed checklists
    - Verification steps
    - Common mistakes to check

---

## 5. DOCUMENTATION STRUCTURE ISSUES

### Missing Cross-References
- Rules don't reference skills for detailed examples
- Skills don't reference rules for quick reference
- No "see also" sections

### Inconsistent Detail Level
- Some rules are very detailed (fetch plans)
- Others are brief (security)
- Skills vary in depth

### No Quick Reference
- No single-page cheat sheet
- No decision tree ("when to use what")
- No troubleshooting guide

---

## 6. SPECIFIC FILE RECOMMENDATIONS

### `.cursorrules`
- Add exception for `@Autowired` in views
- Change EntityManager from absolute to conditional
- Clarify Lombok policy

### `003-services-rule.mdc`
- Add EntityManager usage section
- Clarify field injection policy
- Add bean naming requirement emphasis

### `002-views-rule.mdc`
- Add dependency injection section
- Clarify `@Autowired` vs `@ViewComponent`
- Add constructor injection note

### `007-dto-rule.mdc`
- Add "MUST use prefix" requirement
- Update examples to show prefix
- Add to checklist

### `005-testing-rule.mdc`
- Add prominent `@Transactional` warning
- Add complete `SystemAuthenticator` example
- Clarify E2E test isolation

### `009-fetchplans-rule.mdc`
- Add "always specify explicitly" rule
- Clarify default behavior
- Add performance notes

---

## 7. PRIORITY ACTION ITEMS

### Immediate (Before Next AI Session)
1. ✅ Resolve `@Autowired` contradiction
2. ✅ Add DTO prefix requirement
3. ✅ Update EntityManager rule

### Short Term (This Week)
4. ✅ Add view dependency injection section
5. ✅ Add testing transaction warning
6. ✅ Standardize fetch plan guidance

### Medium Term (This Month)
7. ✅ Add EnumUtils to rules
8. ✅ Clarify Liquibase UUID variable
9. ✅ Standardize changeset ID format
10. ✅ Add cross-references

---

## Conclusion

The documentation is **generally well-structured** but has **critical contradictions** around dependency injection that must be resolved immediately. The **missing information** around DTO prefixes and view dependency injection could cause significant issues.

**Overall Quality:** 7/10
- **Strengths:** Comprehensive coverage, good examples, clear structure
- **Weaknesses:** Contradictions, missing critical details, inconsistent emphasis

**Recommendation:** Address HIGH PRIORITY items before next major development cycle to prevent AI agent confusion and code inconsistencies.
