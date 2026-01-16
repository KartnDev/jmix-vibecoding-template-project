# AI Agents Petclinic Benchmark

## Task Description
**Prompt:** "Can you write a petclinic in this project"
**Expected:** Full Jmix petclinic application with entities, views, i18n, liquibase, services, security

---

## Evaluation Metrics

### 1. Compilation & Runtime (0-10)
- Does the project compile without errors?
- Does the application start successfully?

### 2. Entities Completeness (0-10)
| Expected Entity | Has Entity | Has Relations | Has Annotations |
|-----------------|------------|---------------|-----------------|
| Owner           | ?          | ?             | ?               |
| Pet             | ?          | ?             | ?               |
| PetType         | ?          | ?             | ?               |
| Visit           | ?          | ?             | ?               |
| Vet             | ?          | ?             | ?               |
| Specialty       | ?          | ?             | ?               |

### 3. Views/Screens Completeness (0-10)
| Expected View    | List View | Detail View | Working? |
|------------------|-----------|-------------|----------|
| Owner            | ?         | ?           | ?        |
| Pet              | ?         | ?           | ?        |
| PetType          | ?         | ?           | ?        |
| Visit            | ?         | ?           | ?        |
| Vet              | ?         | ?           | ?        |
| Specialty        | ?         | ?           | ?        |

### 4. Liquibase/DB Schema (0-10)
- Correct table definitions?
- Foreign keys present?
- Indexes created?
- Migrations are idempotent?

### 5. i18n / Localization (0-10)
- Entity messages present?
- View messages present?
- Menu messages present?
- Multiple languages?

### 6. Menu Integration (0-10)
- All views in menu?
- Logical structure?
- Working navigation?

### 7. Following AI Rules/Context (0-10)
- Did agent read CLAUDE.md/AI_CONTEXT.md?
- Did agent follow Jmix conventions from rules?
- Did agent use skills for guidance?

### 8. Code Quality (0-10)
- Proper Jmix annotations?
- Correct imports?
- Proper FetchType usage?
- @InstanceName present?
- @Composition/@OnDelete correct?

### 9. Services/Business Logic (0-10)
- Any services created?
- DTOs if needed?
- Fetch plans defined?

### 10. Testing (0-10)
- Unit tests present?
- Integration tests?
- UI tests?

---

## Results Table

| Agent | Compile | Entities | Views | Liquibase | i18n | Menu | Rules | Quality | Services | Tests | **TOTAL** | Prompts |
|-------|---------|----------|-------|-----------|------|------|-------|---------|----------|-------|-----------|---------|
| **Junie (GPT-5.1 Codex Max)** | 0→5 | 7 | 3→6 | 9 | 7 | 1→5 | 0 | 5→6 | 0 | 0 | **34→45** | 1→6 |
| **Junie (Opus 4.5)** | 7 | 8 | 7 | 9 | 7 | 6 | 1 | 7 | 0 | 0 | **52** | 1 |
| **Junie (Sonnet 4.5)** | 8 | 6 | 8 | 9 | **0** | 6 | 1 | 7 | 0 | 0 | **45** | 1 |
| **Junie (Gemini 3 Flash)** | 9 | 9 | 8 | 8 | **10** | 9 | 3 | 9 | 0 | 1 | **66** | 1 |
| **Junie (Gemini 3 Pro)** | - | - | - | - | - | - | - | - | - | - | **SKIP** | - |
| **Cursor (Default/Auto)** | 6 | 8 | 2 | 7 | 7 | 6 | 1 | 3 | 0 | 0 | **40** ❌ | 5-6 |
| **Cursor (ChatGPT 5.2)** | 9 | 7 | 7 | 7 | 8 | 7 | 3 | 8 | 0 | 0 | **56** | ~1.5 |
| **Cursor (Sonnet 4.5)** | 10 | 10 | 8 | 9 | 9 | 10 | 4 | 10 | 0 | 0 | **70** | 1 |
| **Cursor (Opus 4.5)** | 9 | 10 | 9 | 9 | 9 | 10 | 5 | 10 | 0 | 0 | **71** 🏆 | 2 |
| **Cursor (Gemini 3 Pro)** | | | | | | | | | | | **/100** | |
| **Cursor (Gemini 3 Flash)** | 5 | 8 | 4 | 9 | 4 | 7 | 4 | 5 | 0 | 0 | **46** ❌ | 2 |
| **Continue (Opus 4.5)** | 9 | 9 | 9 | 8 | 10 | 9 | 5 | 9 | 0 | 0 | **68** 💸 | ~1 ($8!) |
| **Continue (Haiku 3.5)** | 0 | 4 | 2 | 5 | 8 | 6 | 3 | 2 | 0 | 0 | **30** 🔴 | 1 (FAIL) |
| **Claude Code (Sonnet 4.5)** | 10 | 15 | 15 | 10 | 10 | 5 | -5 | 10 | 0 | 0 | **65** ⚠️ | 1 |
| **Claude Code (Opus 4.5)** | 4 | 10 | 6 | 7 | 9 | 4 | 4 | 7 | 0 | 0 | **51** ⚠️ | 1 |
| **Claude Code (Haiku 3.5)** | 8 | 8 | 7 | 9 | 9 | 5 | 4 | 7 | 0 | 0 | **57** ✅ | 3 |

---

## Detailed Assessment: Junie (ChatGPT 5.1 Codex Max)

### Test Methodology
- **Initial prompt:** "Can you write a petclinic in this project"
- **Additional prompts:** 5 more (total 6)
- **Score format:** Initial→Final (after iterations)

---

### CRITICAL FINDINGS: Junie Platform Limitations

#### 1. Cannot Read Dotfiles/Dotfolders
- **Junie CANNOT access files/folders starting with `.`**
- `.cursor/`, `.cursorrules`, `.continuerules` - ALL INACCESSIBLE
- **Workaround required:** Had to create separate `ai-docs/` folder
- **Conclusion:** `.cursor` folder is a BAD SOLUTION for Junie (use `ai-docs/` instead)

#### 2. Does NOT Proactively Read AI Context
- Junie does NOT automatically read `AGENTS.md`, `CLAUDE.md`, `AI_CONTEXT.md`
- Must EXPLICITLY ask: "Read AGENTS.md and follow the rules"
- Does NOT discover or use `ai-docs/rules/` or `ai-docs/skills/`
- **Zero autonomous context discovery**

#### 3. Does NOT Self-Verify
- Junie does NOT test its own code until explicitly asked
- Had to request: "Check with Playwright if screens work"
- After verification - fixed ~50% of screen issues
- **Still left 2 runtime errors** (tagPicker component missing)

---

### Score Progression (6 prompts)

| Metric | Prompt 1 | Prompt 6 | Delta |
|--------|----------|----------|-------|
| Compilation | 0 | 5 | +5 |
| Views | 3 | 6 | +3 |
| Menu | 1 | 5 | +4 |
| Quality | 5 | 6 | +1 |
| **TOTAL** | **34** | **45** | **+11** |

---

### Compilation & Runtime: **0→5/10**
**Initial (Prompt 1):**
- FAILS TO COMPILE
- Error: `cannot find symbol class Composition`
- Wrong import: `io.jmix.core.entity.annotation.Composition`

**After iterations (Prompt 6):**
- Compiles successfully
- Application starts
- **2 runtime errors remain:** tagPicker component not found in 2 views

### Entities Completeness: **7/10**
| Entity    | Created | Relations | Annotations |
|-----------|---------|-----------|-------------|
| Owner     | YES     | YES (pets) | YES         |
| Pet       | YES     | YES (owner, type, visits) | YES |
| PetType   | YES     | NO        | YES         |
| Visit     | YES     | YES (pet) | YES         |
| Vet       | YES     | YES (specialties) | YES   |
| Specialty | YES     | YES (vets M2M) | YES      |

**Issues:**
- Wrong import for @Composition annotation
- Duplicate @InstanceName in Pet.java (line 39 and 115)

### Views/Screens Completeness: **3/10**
| View       | List | Detail | In Menu |
|------------|------|--------|---------|
| Owner      | YES  | YES    | NO      |
| Pet        | NO   | NO     | NO      |
| PetType    | YES  | YES    | NO      |
| Visit      | NO   | NO     | NO      |
| Vet        | NO   | NO     | NO      |
| Specialty  | NO   | NO     | NO      |

**Only 2 of 6 entities have views!**

### Liquibase/DB Schema: **9/10**
- All tables created correctly
- Foreign keys present
- Indexes created (IDX_OWNER_LAST_NAME, IDX_VET_LAST_NAME, etc.)
- Proper changeset structure
- Uses ${uuid.type} variable correctly

### i18n / Localization: **7/10**
- All entity messages present
- View messages for Owner and PetType only
- Only English locale
- Missing messages for Pet, Visit, Vet, Specialty views

### Menu Integration: **1/10**
- Only User view in menu
- No petclinic entities in menu at all!
- Menu structure not updated

### Following AI Rules/Context: **2/10**
- Did NOT follow rules for views (should create all CRUD views)
- Did NOT follow menu integration rules
- Partially followed entity conventions
- Wrong annotation import shows lack of Jmix knowledge

### Code Quality: **5/10**
- Proper @JmixEntity annotations
- Proper @InstanceName with @DependsOnProperties
- WRONG import for @Composition
- Duplicate annotations in Pet.java
- FetchType.LAZY used correctly

### Services/Business Logic: **0/10**
- No services created
- No DTOs
- No fetch plans defined

### Testing: **0/10**
- Only default User tests (not petclinic related)
- No tests for petclinic entities

---

### **TOTAL: Junie (GPT-5.1 Codex Max)**

| Metric | Initial | Final | Notes |
|--------|---------|-------|-------|
| Compilation | 0 | 5 | Fixed after explicit request |
| Entities | 7 | 7 | No change |
| Views | 3 | 6 | Added missing views after Playwright test |
| Liquibase | 9 | 9 | No change |
| i18n | 7 | 7 | No change |
| Menu | 1 | 5 | Added after explicit request |
| Rules Following | 0 | 0 | NEVER read AI rules autonomously |
| Quality | 5 | 6 | Minor fixes |
| Services | 0 | 0 | No change |
| Tests | 0 | 0 | No change |
| **TOTAL** | **34/100** | **45/100** | **+11 points after 5 extra prompts** |

---

## Summary Issues for Junie (GPT-5.1 Codex Max)

### Critical
1. **Project does not compile** - wrong @Composition import
2. **Only 2/6 views created** - incomplete functionality
3. **Menu not updated** - users can't access new features

### Major
4. Duplicate @InstanceName in Pet entity
5. No services or business logic
6. No tests for new entities
7. Didn't follow AI context rules

### Minor
8. Only English locale
9. No fetch plans defined
10. Detail views don't show nested collections (pets in owner, visits in pet)

---

## Next Agent to Test
- [ ] Junie (Sonnet 4.5)
- [ ] Junie (Gemini 3 Flash)
- [ ] Junie (Gemini 3 Pro)
- [ ] Cursor (Default)
- [ ] Cursor (ChatGPT)
- [ ] Cursor (Sonnet 4.5)
- [ ] Cursor (Gemini 3 Pro/Flash)
- [ ] Continue (Anthropic API)

---

## Commands Before Each Test
```bash
# Reset project to clean state
cd /Users/dmitrycherkasov/IdeaProjects/migrationcuba/shamrock-jmix-migrated/jmix-petclinic-junie
git reset --hard HEAD
```

---

## Recommendations for AI Rules Structure

Based on Junie testing, different agents need different approaches:

### Agent Compatibility Matrix

| File/Folder | Claude Code | Cursor | Junie | Continue | Copilot |
|-------------|-------------|--------|-------|----------|---------|
| `CLAUDE.md` | AUTO-READ | - | NOT READ | - | - |
| `AGENTS.md` | AUTO-READ | **AUTO-READ** ✅ | **NOT READ** | - | - |
| `AI_CONTEXT.md` | AUTO-READ | - | NOT READ | - | - |
| `.junie/guidelines.md` | - | - | **AUTO-READ** | - | - |
| `ai-docs/rules/` | MANUAL | MANUAL | MANUAL | MANUAL | MANUAL |
| `ai-docs/skills/` | MANUAL | MANUAL | MANUAL | MANUAL | MANUAL |
| `.cursorrules` | - | AUTO-READ | NO ACCESS | - | - |
| `.cursor/rules/` | - | AUTO-READ | NO ACCESS | - | - |
| `.continuerules` | - | - | NO ACCESS | AUTO-READ | - |
| `.github/copilot-instructions.md` | - | - | NO ACCESS | - | AUTO-READ |

### Junie Notes
- **`.junie/guidelines.md`** - default file for Junie (configurable but nobody will bother)
- **`AGENTS.md` does NOT work in Junie** - despite claims it's the "future cross-platform standard"
- Junie cannot read dotfiles EXCEPT `.junie/` folder (its own config)

### Recommended Universal Structure
```
project/
├── AI_CONTEXT.md              # Universal (all agents can read)
├── CLAUDE.md                  # Claude Code (auto-read)
├── AGENTS.md                  # Multi-agent (NOT Junie!)
├── .junie/
│   └── guidelines.md          # Junie (auto-read)
├── ai-docs/                   # UNIVERSAL: All agents can access
│   ├── rules/
│   │   ├── entities/RULE.md
│   │   ├── views/RULE.md
│   │   ├── services/RULE.md
│   │   └── ...
│   └── skills/
│       ├── entities/SKILL.md
│       ├── views/SKILL.md
│       └── ...
├── .cursorrules               # Cursor-specific (dotfile)
├── .cursor/                   # Cursor rules/skills (dotfile)
├── .continuerules             # Continue-specific (dotfile)
└── .github/
    └── copilot-instructions.md
```

### Key Insight
**Use `ai-docs/` as the UNIVERSAL rules folder - accessible by ALL agents including Junie**

Dotfiles (`.cursor/`, `.cursorrules`, `.continuerules`) are tool-specific and should reference `ai-docs/`

---

## Conclusions

### Junie Platform Summary

| Aspect | GPT-5.1 Codex | Opus 4.5 | Platform Issue? |
|--------|---------------|----------|-----------------|
| One-shot score | 34/100 | 52/100 | - |
| Final score | 45/100 (6 prompts) | 52/100 (1 prompt) | - |
| Read CLAUDE.md | NO | YES | Model dependent |
| Read ai-docs/* | NO | NO | **PLATFORM BUG** |
| Use MCP tools | NO | NO | **PLATFORM BUG** |
| Self-verify | NO | NO | **PLATFORM BUG** |
| Dotfile access | BROKEN | BROKEN | **PLATFORM BUG** |

### Key Insight

**Junie has a ~50-55 point ceiling** regardless of model strength because:
1. Doesn't proactively read `ai-docs/` rules and skills
2. Doesn't proactively use MCP tools
3. Doesn't self-verify code

**Model quality matters less than platform capabilities**
- Opus is much stronger than GPT
- But only +18 points difference on Junie
- Platform limitations cap the result

---

## Detailed Assessment: Junie (Opus 4.5)

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 1 (one-shot)

### Key Observations

#### What Opus DID:
- Read CLAUDE.md autonomously
- Used existing code (User.java, user-list-view.xml) as reference
- Used Gradle build for compilation check
- Produced working code in one shot

#### What Opus DID NOT (same Junie limitations):
| Tool | Status | Impact |
|------|--------|--------|
| `ai-docs/rules/` | NOT READ | Violated some rules |
| `ai-docs/skills/` | NOT READ | Missing best practices |
| Playwright MCP | NOT USED | No UI verification |
| IDEA MCP | NOT USED | No error/warning check |
| jmix-rag-mcp | NOT USED | No Jmix-specific queries |

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 7 | Minor issues, mostly works |
| Entities | 8 | Good structure, knows Jmix |
| Views | 7 | More complete than GPT |
| Liquibase | 9 | Excellent |
| i18n | 7 | Standard |
| Menu | 6 | Better integration |
| Rules Following | 1 | Only CLAUDE.md, ignored ai-docs/* |
| Quality | 7 | Opus knows Jmix well |
| Services | 0 | None created |
| Tests | 0 | None created |
| **TOTAL** | **52/100** | One-shot |

### Verdict: Junie + Opus

**Platform bottleneck confirmed.**

- Opus is strong (knows Jmix, good code quality)
- But Junie limits it:
  - Doesn't proactively read `ai-docs/` rules and skills
  - Doesn't use available MCP tools
  - Only reads CLAUDE.md, ignores detailed rules/skills in `ai-docs/`

**Result:** 52/100 in one shot vs GPT's 34/100 in one shot
- **+18 points** advantage from model strength alone
- But still limited by platform (~50% ceiling without MCP usage)

---

## Detailed Assessment: Junie (Sonnet 4.5)

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 1 (one-shot)

### What Sonnet Did
- Read `.junie/guidelines.md` (auto-loaded)
- Created 5 entities (Owner, Pet, PetType as enum, Veterinarian, Visit)
- **Missing:** Specialty entity (упростил архитектуру)
- UI views better than Opus visually

### Critical Issue: i18n = 0/10
- Used `msg://` keys correctly in XML
- **FORGOT to add actual messages to properties file!**
- Result: UI shows raw translation keys instead of text

### Architecture Decisions
| Aspect | Sonnet Choice | Correct? |
|--------|---------------|----------|
| PetType | **Enum** | ⚠️ Упрощение |
| Specialty | **String field** in Vet | ⚠️ Упрощение |
| Vet naming | Veterinarian (verbose) | OK |

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 8 | Works after DB cleanup |
| Entities | 6 | 5/6, simplified model |
| Views | 8 | Good UI, better than Opus |
| Liquibase | 9 | Correct structure |
| **i18n** | **0** | 🔴 Complete failure |
| Menu | 6 | Basic integration |
| Rules | 1 | Only guidelines.md |
| Quality | 7 | Good code |
| Services | 0 | None |
| Tests | 0 | None |
| **TOTAL** | **45/100** | One-shot |

### Verdict: Junie + Sonnet

**i18n failure killed the score.** Otherwise would be ~55-60.

- UI quality good
- Architecture oversimplified
- Fundamental miss on localization

---

## Detailed Assessment: Junie (Gemini 3 Flash) 🏆

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 1 (one-shot)

### What Gemini Did RIGHT
- ✅ Read `.junie/guidelines.md` (auto-loaded)
- ✅ Read `ai-docs/skills/i18n/SKILL.md` (proactively!)
- ✅ All 6 entities with correct architecture
- ✅ PetType and Specialty as **separate entities** (like Opus)
- ✅ M2M relationship Vet ↔ Specialty
- ✅ **ALL messages added** - perfect i18n
- ✅ Security role created (PetclinicRole)
- ✅ Started writing tests
- ✅ Knows about `rm -rf .jmix/hsqldb/*` for DB cleanup

### Single Issue
- ❌ Missing Pet.list view → entityPicker broken in forms

### Architecture (Correct!)
| Aspect | Gemini Choice | Correct? |
|--------|---------------|----------|
| PetType | **Entity** (table) | ✅ |
| Specialty | **Entity** + M2M | ✅ |
| Vet naming | Vet (concise) | ✅ |

### Code Quality Highlights
```java
// Proper M2M with link table
@JoinTable(name = "VET_SPECIALTY_LINK",
    joinColumns = @JoinColumn(name = "VET_ID"),
    inverseJoinColumns = @JoinColumn(name = "SPECIALTY_ID"))
@ManyToMany
private Set<Specialty> specialties;

// Proper @InstanceName
@InstanceName
@DependsOnProperties({"firstName", "lastName"})
public String getDisplayName() {
    return String.format("%s %s", firstName, lastName);
}
```

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 9 | Works perfectly |
| Entities | 9 | All 6, correct architecture |
| Views | 8 | -1 missing Pet.list, -1 simplicity |
| Liquibase | 8 | Works, but uses `UUID` not `${uuid.type}` |
| **i18n** | **10** | 🟢 Perfect - all messages! |
| Menu | 9 | Separate Petclinic section |
| Rules | 3 | Read ai-docs/skills/i18n! |
| Quality | 9 | Excellent code |
| Services | 0 | None |
| Tests | 1 | Started! |
| **TOTAL** | **66/100** 🏆 | One-shot, BEST RESULT |

### Verdict: Junie + Gemini 3 Flash

**WINNER for Junie platform.**

- Best ROI (cheapest model, best result)
- Only model that proactively read ai-docs/skills/
- Perfect i18n (others failed or missed)
- Correct architecture (not oversimplified)

---

## Updated Conclusions

### Junie Platform - Final Ranking

| Rank | Model | Score | Cost | ROI |
|------|-------|-------|------|-----|
| 🥇 | **Gemini 3 Flash** | 66/100 | 💰 Low | 🔥 Best |
| 🥈 | Opus 4.5 | 52/100 | 💰💰💰 High | Medium |
| 🥉 | Sonnet 4.5 | 45/100 | 💰💰 Medium | Poor (i18n fail) |
| 4 | GPT-5.1 Codex | 45/100 | 💰💰 Medium | Poor (6 prompts) |

### Key Insights

1. **Gemini 3 Flash is the Junie winner**
   - Cheapest model
   - Highest score
   - Only one that read ai-docs proactively

2. **i18n is the differentiator**
   - Gemini: 10/10 (perfect)
   - Sonnet: 0/10 (complete fail)
   - This 10-point swing determined ranking

3. **Price ≠ Quality on Junie**
   - Opus (expensive) < Gemini Flash (cheap)
   - Platform limitations matter more than model strength

4. **Gemini reads more context**
   - Read ai-docs/skills/i18n/SKILL.md
   - Other models ignored ai-docs entirely

### Commands Before Each Test (Updated)

```bash
# Full reset for clean test
cd /path/to/project
git reset --hard HEAD
git clean -fd
rm -rf .jmix/hsqldb/*    # CRITICAL: Clean embedded DB!
```

### What Still Needs Testing
- [x] ~~Junie (Gemini 3 Pro)~~ - SKIPPED (price similar to Opus, Flash is better ROI)
- [x] Cursor (Default) - DONE ❌ 40/100
- [x] Cursor (ChatGPT 5.2) - DONE ✅ 56/100
- [x] Cursor (Sonnet 4.5) - DONE ✅ 70/100
- [x] Cursor (Opus 4.5) - DONE 🏆 **71/100 NEW LEADER**
- [ ] Cursor (Gemini 3 Flash)
- [ ] Continue (Anthropic API)
- [ ] Claude Code (Opus 4.5)

---

## Detailed Assessment: Cursor (Default/Auto)

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 5-6 (could not finish)

### Platform Observations
- ✅ Reads AGENTS.md (unlike Junie!)
- ✅ Has built-in linter integration
- ✅ Uses Playwright for testing
- ❌ Does NOT use MCP for self-verification
- ❌ Cheap auto-model produces broken code

### Critical Issues
1. **entityPicker without actions** - no lookup/clear buttons, fields unusable
2. **vet-detail missing specialties field** - M2M relation created but not in UI
3. **5-6 prompts couldn't fix issues** - wall-hitting behavior

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 6 | Compiles but runtime UX broken |
| Entities | 8 | All 6, correct architecture |
| Views | 2 | **BROKEN** - entityPicker no controls |
| Liquibase | 7 | Good structure with preConditions |
| i18n | 7 | All messages, EN only |
| Menu | 6 | Has Reference Data submenu |
| Rules | 1 | Read AGENTS.md but didn't apply |
| Quality | 3 | Code exists but **doesn't work** |
| Services | 0 | None |
| Tests | 0 | None |
| **TOTAL** | **40/100** | ❌ 5-6 prompts, still broken |

### Verdict: Cursor + Default Model

**WORST RESULT.** Cheap model + platform = useless.

- Worse than Junie (Sonnet/GPT) which at least produced working code
- 5-6 prompts wasted with no result
- Wall-hitting behavior is frustrating
- **Not recommended for anything**

---

## Detailed Assessment: Cursor (ChatGPT Codex 5.2)

### Test Methodology
- **Prompt:** Structured multi-step prompt with inspection + creation + verification
- **Prompts needed:** ~1.5 (one hint about dirty DB)

### Platform + Model Observations
- ✅ Reads AGENTS.md and rules
- ✅ **Thinks about components** - considered entityComboBox vs multiSelectComboBox
- ✅ Uses built-in linter
- ✅ **0 runtime errors** - best runtime stability
- ❌ Does NOT use MCP for self-verification
- ❌ Didn't detect dirty DB state (needed hint)

### What ChatGPT Did RIGHT
- `multiSelectComboBox` for M2M Vet↔Specialty (correct component!)
- `entityPicker` WITH actions (lookup + clear)
- @Composition + @OnDelete(CASCADE) on Pet.visits
- Set<Specialty> instead of List (correct for M2M)
- Liquibase with DROP tables (after hint)
- `final` parameters in setters (style)

### Architecture Simplification
| Aspect | Full Petclinic | ChatGPT Choice | Impact |
|--------|----------------|----------------|--------|
| Visit → Vet | ✅ Has relation | ❌ **Missing** | Vet entity useless |
| Menu structure | Submenu groups | Flat list | Minor |
| Security Role | PetclinicRole | ❌ None | Missing |
| Owner.pets in detail | Nested dataGrid | ❌ None | Missing |

**Visit has no Vet relation** → Vet entity created but not connected to business logic.

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 9 | Works first try |
| Entities | 7 | All 6 but Visit↔Vet missing |
| Views | 7 | All work, multiSelectComboBox correct |
| Liquibase | 7 | Good after hint (DROP + create) |
| i18n | 8 | All messages, EN only |
| Menu | 7 | Flat but functional |
| Rules | 3 | Reads AGENTS.md, thinks about components |
| Quality | 8 | Good code, correct patterns |
| Services | 0 | None |
| Tests | 0 | None |
| **TOTAL** | **56/100** | ~1.5 prompts, **0 runtime errors** |

### Verdict: Cursor + ChatGPT 5.2

**Solid middle ground.** Not the best score, but:
- **0 runtime errors** - best stability
- Thinks about correct components
- Simplified architecture (acceptable for vibe-coding)
- Better than Cursor Default by a mile

---

## Cursor Platform Summary

| Model | Score | Prompts | Runtime | Verdict |
|-------|-------|---------|---------|---------|
| Default/Auto | 40/100 | 5-6 | ❌ Broken | **Avoid** |
| ChatGPT 5.2 | 56/100 | ~1.5 | ✅ 0 errors | Good for vibe-coding |

### Cursor vs Junie

| Aspect | Cursor | Junie |
|--------|--------|-------|
| Reads AGENTS.md | ✅ YES | ❌ NO |
| Built-in linter | ✅ YES | ❌ NO |
| Playwright usage | ✅ YES | ⚠️ Manual only |
| MCP proactive use | ❌ NO | ❌ NO |
| Model ceiling | Higher | ~55 max |

**Cursor platform is better** - reads more context, has linter.
**But model quality matters** - Default model is garbage.

---

## Detailed Assessment: Cursor (Sonnet 4.5) 🏆

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 1 (ONE-SHOT!)

### What Sonnet Did — EXCEPTIONAL

#### Context Reading
- ✅ Read AGENTS.md automatically
- ✅ Read .cursorrules automatically
- ✅ Acknowledged detailed docs exist (didn't read ai-docs/*, but followed patterns)

#### Architecture — COMPLETE
| Aspect | Implementation | Score |
|--------|----------------|-------|
| Visit → Vet | ✅ **Full relation** | Perfect |
| Vet ↔ Specialty | ✅ M2M with link table | Perfect |
| @Index in entities | ✅ IDX_VISIT_PET, IDX_VET_LAST_NAME, etc. | Bonus |
| PetClinicRole | ✅ Full security role | Bonus |
| Sample data | ✅ Liquibase INSERT statements | Bonus |
| Documentation | ✅ PETCLINIC_BUILD.md | Bonus |

#### Code Quality Highlights

```java
// Indexes directly in @Table annotation
@Table(name = "VISIT", indexes = {
    @Index(name = "IDX_VISIT_PET", columnList = "PET_ID"),
    @Index(name = "IDX_VISIT_VET", columnList = "VET_ID"),
    @Index(name = "IDX_VISIT_DATE", columnList = "VISIT_DATE")
})

// Complete security role
@ResourceRole(name = "PetClinic: full access", code = PetClinicRole.CODE)
public interface PetClinicRole {
    @EntityPolicy(entityClass = Visit.class, actions = {EntityPolicyAction.ALL})
    @EntityAttributePolicy(entityClass = Visit.class, attributes = "*", action = EntityAttributePolicyAction.MODIFY)
    void visit();

    @ViewPolicy(viewIds = {"Visit.list", "Visit.detail"})
    void visitViews();

    @MenuPolicy(menuIds = {"PetType.list", "Owner.list", "Pet.list", ...})
    void petClinicMenus();
}
```

#### Menu Structure — Excellent
```xml
<menu id="petClinic" opened="true">
    <item view="Owner.list"/>
    <item view="Pet.list"/>
    <item view="Visit.list"/>
    <separator/>  <!-- Logical grouping! -->
    <item view="Vet.list"/>
    <item view="Specialty.list"/>
    <item view="PetType.list"/>
</menu>
```

#### Sample Data in Liquibase
- 6 pet types (Cat, Dog, Bird, Lizard, Snake, Hamster)
- 3 owners with full contact info
- 4 pets linked to owners and types
- 3 specialties (Radiology, Surgery, Dentistry)
- 3 vets with M2M specialties
- 3 visits with pet and vet references

### Minor Issues
| Issue | Impact | Severity |
|-------|--------|----------|
| `entityPicker` for M2M specialties | Should be `multiSelectComboBox` | Minor |
| `UUID` instead of `${uuid.type}` | Portability concern | Minor |

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 10 | 0 errors, works perfectly |
| Entities | 10 | All 6, complete architecture, @Index annotations |
| Views | 8 | All work, entityPicker for M2M (minor issue) |
| Liquibase | 9 | Sample data! UUID not ${uuid.type} |
| i18n | 9 | All messages present, EN only |
| Menu | 10 | Perfect structure with separator |
| Rules | 4 | Reads AGENTS.md + .cursorrules, follows patterns |
| Quality | 10 | Security role, documentation, indexes |
| Services | 0 | None |
| Tests | 0 | None |
| **TOTAL** | **70/100** 🏆 | **ONE-SHOT, NEW LEADER** |

### Verdict: Cursor + Sonnet 4.5

**BEST RESULT. New benchmark leader.**

What makes this exceptional:
1. **Complete architecture** — Visit → Vet relation (others missed this)
2. **Security role** — only Gemini Flash also did this
3. **Sample data** — ready to use immediately
4. **Documentation** — full PETCLINIC_BUILD.md with compliance checklist
5. **@Index annotations** — performance optimization in entities

### Sonnet 4.5: Junie vs Cursor

| Aspect | Junie (Sonnet 4.5) | Cursor (Sonnet 4.5) |
|--------|--------------------|--------------------|
| Score | 45/100 | **70/100** |
| i18n | ❌ 0/10 (forgot messages) | ✅ 9/10 |
| Security | ❌ None | ✅ Full role |
| Sample data | ❌ None | ✅ Complete |
| Documentation | ❌ None | ✅ Full MD |
| Architecture | Simplified | Complete |

**+25 points difference!** Same model, different platform.

**Junie limits Sonnet severely.** Cursor unlocks its full potential.

---

## Detailed Assessment: Cursor (Opus 4.5) 🏆

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 2 (first attempt failed on VaadinIcons.PAW)

### What Opus Did — BEST PRACTICES

#### Context Reading — EXCEPTIONAL
- ✅ Read AGENTS.md automatically
- ✅ Read .cursorrules automatically
- ✅ **Actually read ai-docs/skills/*.md files!** (entities, views, liquibase, i18n)
- ✅ **Self-verified via Playwright MCP** — opened browser to test

#### Improvements Over Sonnet
| Aspect | Sonnet | Opus |
|--------|--------|------|
| M2M component | entityPicker (wrong) | **multiSelectComboBox** (correct) |
| @DependsOnProperties | Missing on Vet | ✅ Present |
| Menu icons | None | ✅ vaadin:hospital, vaadin:users, etc |
| Menu structure | Flat with separator | **Dictionaries submenu** |
| i18n structure | Plain | **Commented sections** |
| Self-verification | None | ✅ **Playwright browser test** |
| ai-docs reading | Acknowledged | ✅ **Actually read SKILL.md files** |

#### Code Quality Highlights

```xml
<!-- Menu with icons and logical grouping -->
<menu id="petclinic" title="msg://..." icon="vaadin:hospital">
    <item view="Owner.list" icon="vaadin:users"/>
    <item view="Pet.list" icon="vaadin:heart"/>
    <item view="Visit.list" icon="vaadin:calendar"/>
    <item view="Vet.list" icon="vaadin:specialist"/>
</menu>
<menu id="dictionaries" title="msg://..." icon="vaadin:book">
    <item view="PetType.list"/>
    <item view="Specialty.list"/>
</menu>
```

```xml
<!-- Correct multiSelectComboBox for M2M -->
<multiSelectComboBox id="specialtiesField" property="specialties"
                     itemsContainer="specialtiesDc"/>
```

```properties
# Structured i18n with comments
# Owner entity
com.company.jmixpetclinicjunie.entity/Owner=Owner
...
# Pet entity
com.company.jmixpetclinicjunie.entity/Pet=Pet
```

### Minor Issues
| Issue | Impact | Severity |
|-------|--------|----------|
| VaadinIcons.PAW not found | Required 2nd prompt | Minor |
| `UUID` instead of `${uuid.type}` | Portability | Minor |
| Only EN locale | Incomplete i18n | Minor |

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 9 | 2nd prompt due to icon issue |
| Entities | 10 | All 6, @Index, @DependsOnProperties |
| Views | 9 | **multiSelectComboBox correct!** |
| Liquibase | 9 | Sample data, UUID not ${uuid.type} |
| i18n | 9 | Commented structure, EN only |
| Menu | 10 | Icons + Dictionaries submenu |
| Rules | 5 | **Read ai-docs/skills/*!** |
| Quality | 10 | Security role, Playwright verify |
| Services | 0 | None |
| Tests | 0 | None |
| **TOTAL** | **71/100** 🏆 | **NEW LEADER** |

### Verdict: Cursor + Opus 4.5

**BEST RESULT. New benchmark leader.**

What makes this exceptional:
1. **Actually reads ai-docs/** — only agent to do this properly
2. **Self-verifies via Playwright** — opened browser to test
3. **Correct M2M component** — multiSelectComboBox
4. **Menu with icons** — professional look
5. **Structured i18n** — commented sections

### Opus 4.5: Junie vs Cursor

| Aspect | Junie (Opus 4.5) | Cursor (Opus 4.5) |
|--------|------------------|-------------------|
| Score | 52/100 | **71/100** |
| Read ai-docs | ❌ No | ✅ Yes |
| Self-verify | ❌ No | ✅ Playwright |
| M2M component | ? | ✅ multiSelectComboBox |
| Menu icons | ❌ No | ✅ Yes |

**+19 points difference!** Same model, different platform.

---

## Final Rankings (Updated)

| Rank | Agent | Score | Prompts | Key Strength |
|------|-------|-------|---------|--------------|
| 🥇 | **Cursor (Opus 4.5)** | **71/100** | 2 | Reads ai-docs + Playwright verify |
| 🥈 | Cursor (Sonnet 4.5) | 70/100 | 1 | One-shot + complete architecture |
| 🥉 | Junie (Gemini 3 Flash) | 66/100 | 1 | Best i18n on Junie |
| 4 | Cursor (ChatGPT 5.2) | 56/100 | ~1.5 | 0 runtime errors |
| 5 | Junie (Opus 4.5) | 52/100 | 1 | Knows Jmix well |
| 6 | Junie (Sonnet 4.5) | 45/100 | 1 | Good UI, failed i18n |
| 7 | Junie (GPT-5.1 Codex) | 45/100 | 6 | Needed many iterations |
| 8 | Cursor (Default) | 40/100 | 5-6 | Broken, avoid |

## Key Insights (Final)

### Platform Comparison
| Factor | Junie | Cursor |
|--------|-------|--------|
| AGENTS.md | ❌ NO | ✅ YES |
| .cursorrules | N/A | ✅ YES |
| ai-docs/* | ⚠️ Only Gemini | ✅ Opus reads! |
| Built-in linter | ❌ NO | ✅ YES |
| Playwright MCP | ❌ NO | ✅ Opus uses! |
| Model ceiling | ~55-66 | **~71+** |

### Model Quality on Each Platform

**Same model, different results:**
- Sonnet 4.5: Junie 45 → Cursor 70 (+25!)
- Opus 4.5: Junie 52 → Cursor 71 (+19!)
- The platform matters more than we thought

### Recommendations

1. **Best overall:** Cursor + Opus 4.5 (71/100) — reads ai-docs, self-verifies
2. **Best one-shot:** Cursor + Sonnet 4.5 (70/100) — perfect balance
3. **Best budget:** Junie + Gemini 3 Flash (66/100)
4. **Avoid:** Cursor Default, Junie + GPT

### Key Finding: Cursor Opus is Special

Only Cursor + Opus 4.5 did ALL of these:
- ✅ Read ai-docs/skills/* documentation
- ✅ Self-verified via Playwright browser
- ✅ Used correct multiSelectComboBox for M2M
- ✅ Added menu icons
- ✅ Created structured i18n with comments

This suggests Opus needs a capable platform to show its full potential.

---

## Detailed Assessment: Cursor (Gemini 3 Flash) ❌

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 2 (1st had undefined view, 2nd used Playwright to check)

### What Gemini CLAIMED to Do
> "Да, я ознакомился с документацией. Перед началом работы я изучил:
> Инструкции проекта (файлы в ai-docs/ и правила в .cursorrules)..."

**Reality:** Cursor auto-feeds rules/skills to model, but Gemini didn't apply them correctly.

### Critical Issues Found

#### 1. Runtime Error: `itemsQuery` missing `class` attribute
```xml
<!-- BROKEN CODE -->
<entityComboBox id="typeField" property="type">
    <itemsQuery>  <!-- ERROR: missing class attribute -->
        <![CDATA[select e from PetType e]]>
    </itemsQuery>
</entityComboBox>

<!-- CORRECT CODE -->
<entityComboBox id="typeField" property="type">
    <itemsQuery class="com.company.jmixpetclinicjunie.entity.PetType">
        <query><![CDATA[select e from PetType e]]></query>
    </itemsQuery>
</entityComboBox>
```

#### 2. Messages in Wrong Format
- Views use: `msg://petDetailView.title`
- Messages added to main `messages_en.properties` with short keys
- But `msg://` resolves to package-local path → **keys not found**

```properties
# Added to main file (WRONG location)
petDetailView.title=Pet

# Should be:
com.company.jmixpetclinicjunie.view.pet/petDetailView.title=Pet
# OR create local messages.properties in each view package
```

### What Was Done Correctly
- ✅ All 6 entities created (Owner, Pet, PetType, Specialty, Vet, Visit)
- ✅ Proper structure: UUID + Version + InstanceName
- ✅ M2M Vet ↔ Specialty with link table
- ✅ Liquibase with FK and indexes
- ✅ Used Playwright to self-check (didn't fix issues though)

### Score Breakdown

| Metric | Score | Notes |
|--------|-------|-------|
| Compilation | 5 | Runtime errors in pet-detail-view |
| Entities | 8 | All 6, correct architecture |
| Views | 4 | itemsQuery broken, messages wrong |
| Liquibase | 9 | Good structure, FK, indexes |
| i18n | 4 | Messages exist but wrong format |
| Menu | 7 | Present, messages may not resolve |
| Rules | 4 | Claims to read, didn't apply correctly |
| Quality | 5 | Code exists but doesn't work |
| Services | 0 | None |
| Tests | 0 | None |
| **TOTAL** | **46/100** ❌ | 2 prompts, **broken views** |

### Verdict: Cursor + Gemini 3 Flash

**DISAPPOINTING.** Gemini Flash on Cursor performs worse than on Junie!

| Aspect | Junie (Gemini 3 Flash) | Cursor (Gemini 3 Flash) |
|--------|------------------------|-------------------------|
| Score | **66/100** 🏆 | 46/100 ❌ |
| i18n | 10/10 (perfect) | 4/10 (wrong format) |
| Views | 8/10 (minor issues) | 4/10 (runtime errors) |
| Self-verify | No | Yes (but didn't fix) |

**Unexpected finding:** Same model performs WORSE on Cursor than Junie!
- Junie: 66/100 (best on platform)
- Cursor: 46/100 (below average)

### Why the Difference?

Hypothesis: **Cursor's auto-feeding of rules may confuse Gemini.**
- Junie: Gemini reads docs itself, understands context
- Cursor: Rules injected automatically, Gemini sees but doesn't understand

Gemini Flash seems to work better with less context injection and more autonomous exploration.

---

## Updated Rankings

| Rank | Agent | Score | Prompts | Key Strength |
|------|-------|-------|---------|--------------|
| 🥇 | **Cursor (Opus 4.5)** | **71/100** | 2 | Reads ai-docs + Playwright verify |
| 🥈 | Cursor (Sonnet 4.5) | 70/100 | 1 | One-shot + complete architecture |
| 🥉 | Junie (Gemini 3 Flash) | 66/100 | 1 | Best i18n on Junie |
| 4 | Cursor (ChatGPT 5.2) | 56/100 | ~1.5 | 0 runtime errors |
| 5 | Junie (Opus 4.5) | 52/100 | 1 | Knows Jmix well |
| 6 | **Cursor (Gemini 3 Flash)** | **46/100** | 2 | ❌ Worse than Junie! |
| 7 | Junie (Sonnet 4.5) | 45/100 | 1 | Good UI, failed i18n |
| 7 | Junie (GPT-5.1 Codex) | 45/100 | 6 | Needed many iterations |
| 9 | Cursor (Default) | 40/100 | 5-6 | Broken, avoid |

### Key Finding: Gemini Flash Platform Paradox

**Gemini 3 Flash performs BETTER on Junie than Cursor!**

| Platform | Score | Difference |
|----------|-------|------------|
| Junie | 66/100 | — |
| Cursor | 46/100 | **-20 points!** |

This is the OPPOSITE of other models (Sonnet, Opus perform better on Cursor).

**Conclusion:** Match model to platform:
- Gemini Flash → Junie (66)
- Sonnet/Opus → Cursor (70-71)

---

## Detailed Assessment: Claude Code (Sonnet 4.5) ⚠️ PSYCHOPATH AGENT

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 1 (ONE-SHOT!)
- **CLAUDE.md:** Read automatically (system reminder)

### What Claude Code Did — EXCEPTIONAL (but also psycho)

#### Context Reading
- ✅ Read CLAUDE.md automatically (system reminder)
- ❌ Did NOT read ai-docs/rules/*.md
- ❌ Did NOT read ai-docs/skills/*.md
- ✅ Used TodoWrite for planning
- ⚠️ Used `gradlew build`/`bootRun` — but only checked "Started" message
- ❌ Did NOT use IDEA MCP (despite CLAUDE.md saying "Recommended")
- ❌ Did NOT use Playwright MCP
- ❌ Did NOT open browser to test views
- ❌ Did NOT verify CRUD operations work

#### 🔴 CRITICAL: Read Instructions, Ignored Them

**CLAUDE.md clearly stated:**
```markdown
### Verification Steps
After writing code, run the application to catch errors:

1. **Run application** - Many design-time errors only visible when app runs
2. **Test in browser** (http://localhost:8080)
   - Navigate to your views
   - Click through all functionality
   - Create, edit, delete records
   - Test pickers, grids, filters

### IDEA MCP — Views Check (Recommended)
open_file_in_editor(filePath)
wait 3-10 seconds
get_file_problems(filePath, onlyErrors=false)
```

**What Claude Code actually did:**
- Ran `gradlew bootRun`
- Saw "Started JmixPetclinicJunieApplication"
- Said "done" 🤡

**Zero real verification.** Got lucky that code worked. Psychopath behavior — read the rules as system reminder, then completely ignored them.

#### Architecture — COMPLETE + CORRECT

| Aspect | Implementation | Score |
|--------|----------------|-------|
| Visit → Vet | ✅ **Full relation!** | Perfect |
| Vet ↔ Specialty | ✅ M2M with VET_SPECIALTY_LINK | Perfect |
| @Index in entities | ✅ IDX_VISIT_PET, IDX_VISIT_DATE, IDX_PET_OWNER, etc. | Bonus |
| Sample data | ✅ Liquibase INSERT statements (8 pets, 4 visits, etc.) | Bonus |
| @InstanceName | ✅ With @DependsOnProperties on Vet | Perfect |
| entityPicker | ✅ WITH entity_lookup + entity_clear actions | Perfect |

#### Code Quality Highlights

```java
// Visit entity with Vet relation (many others missed this!)
@JoinColumn(name = "VET_ID")
@ManyToOne(fetch = FetchType.LAZY)
private Vet vet;

// Proper @Index annotations directly in @Table
@Table(name = "VISIT", indexes = {
    @Index(name = "IDX_VISIT_PET", columnList = "PET_ID"),
    @Index(name = "IDX_VISIT_DATE", columnList = "VISIT_DATE")
})

// Proper @InstanceName with @DependsOnProperties
@InstanceName
@DependsOnProperties({"firstName", "lastName"})
public String getInstanceName() {
    return String.format("%s %s", firstName, lastName);
}
```

```xml
<!-- Correct entityPicker with actions -->
<entityPicker id="typeField" property="type">
    <actions>
        <action id="entityLookup" type="entity_lookup"/>
        <action id="entityClear" type="entity_clear"/>
    </actions>
</entityPicker>

<!-- Proper fetchPlan with nested properties -->
<fetchPlan extends="_base">
    <property name="pet" fetchPlan="_base"/>
    <property name="vet" fetchPlan="_base"/>
</fetchPlan>
```

```properties
# Correct i18n format (full path)
com.company.jmixpetclinicjunie.entity/PetType=Pet Type
com.company.jmixpetclinicjunie.entity/Vet=Veterinarian
com.company.jmixpetclinicjunie.view.visit/VisitDetailView.title=Visit
```

#### Liquibase Structure — Professional

```
liquibase/changelog/
├── 010-init-user.xml    # Existing
├── 020-pettype.xml      # Separate files!
├── 030-specialty.xml
├── 040-owner.xml
├── 050-pet.xml          # With sample data
├── 060-vet.xml          # With M2M link table
└── 070-visit.xml        # Depends on pet + vet
```

Each file has proper structure:
1. createTable changeset
2. createIndex changeset
3. addForeignKeyConstraint changeset
4. insert (sample data) changeset

### Score Breakdown

| Metric | Score | Max | Notes |
|--------|-------|-----|-------|
| Compilation | 10 | 10 | Clean code, no errors |
| Entities | 15 | 15 | All 6, **Visit→Vet!**, @Index |
| Views | 15 | 15 | All 12, correct entityPicker syntax |
| Liquibase | 10 | 10 | Separate files, sample data, FK |
| i18n | 10 | 10 | Correct format, all messages |
| Menu | 5 | 5 | Dictionaries submenu |
| Rules | **-5** | 10 | 🔴 **PENALTY: Ignored CLAUDE.md instructions** |
| Quality | 10 | 10 | Clean, professional code |
| Services | 0 | 5 | None created |
| Tests | 0 | 10 | None created |
| **TOTAL** | **65/100** ⚠️ | 100 | **High output, zero discipline** |

#### Rules Score Breakdown: -5/10
- **-10 points:** Ignored verification steps from CLAUDE.md (read it as system reminder, then completely ignored)
- **-5 points:** Did not read ai-docs/rules/* and ai-docs/skills/* (available, documented in CLAUDE.md)
- **+5 points:** Did read CLAUDE.md (baseline), used TodoWrite
- **Net:** -5/10

**This is a PENALTY score.** Agent actively ignored instructions it received.

### Verdict: Claude Code + Sonnet 4.5

**High output quality, but ZERO discipline. Psychopath agent.**

What makes this exceptional:
1. **Complete architecture** — Visit → Vet relation (Cursor Sonnet also had it, but Claude Code did it cleaner)
2. **Correct entityPicker** — with actions (Cursor ChatGPT/Gemini failed this)
3. **Correct i18n format** — full paths (Cursor Gemini Flash failed this)
4. **Professional Liquibase** — separate files per entity
5. **Sample data** — 8 pets, 4 visits, linked correctly
6. **One-shot** — no iterations needed

**What makes this concerning:**
1. **Read CLAUDE.md but ignored verification instructions** — psychopath behavior
2. **Zero browser testing** — could have runtime errors we don't know about
3. **Zero IDEA MCP usage** — despite "Recommended" in CLAUDE.md
4. **Zero Playwright usage** — could have caught UI bugs
5. **Got lucky** — code happened to work, but process was wrong

**The 80/100 score is real, but the methodology was flawed.**
If there were runtime errors, Claude Code would have shipped broken code with confidence.

### Claude Code Sonnet vs Cursor Sonnet

| Aspect | Cursor (Sonnet 4.5) | Claude Code (Sonnet 4.5) |
|--------|---------------------|-------------------------|
| Raw output quality | 70/100 | 80/100 (before penalty) |
| **Final Score** | **70/100** | **65/100** (after -15 penalty) |
| entityPicker | ✅ Correct | ✅ Correct |
| Visit→Vet | ✅ Yes | ✅ Yes |
| Liquibase structure | Single file | **Separate files** |
| i18n format | ✅ Correct | ✅ Correct |
| Sample data | ✅ Yes | ✅ Yes |
| Security role | ✅ Created | ❌ None |
| Menu icons | ❌ None | ❌ None |
| Rules reading | Reads AGENTS.md + .cursorrules | Reads CLAUDE.md only |
| **Follows instructions** | ✅ Yes | ❌ **IGNORED** |
| **Self-verification** | ⚠️ None | ❌ **None (despite instructions)** |

**Cursor Opus wins.** Claude Code has better raw output but -15 penalty for ignoring rules.

**Why Claude Code has better RAW output:**
1. **Cleaner context** — CLAUDE.md is focused, not overloaded like .cursorrules
2. **TodoWrite** — structured planning helped
3. **Professional structure** — separate Liquibase files

**Why Claude Code LOSES overall:**
1. **-10 penalty:** Ignored verification steps from CLAUDE.md (read it, then ignored)
2. **-5 penalty:** Didn't read ai-docs/rules, ai-docs/skills
3. **Psychopath behavior:** Does what it wants, not what you wrote
4. **Zero self-verification:** Would have shipped broken code with confidence
5. **Untrustworthy:** High variance, can't rely on consistent behavior

| Aspect | Cursor Opus (71) | Claude Code Sonnet (65) |
|--------|------------------|-------------------------|
| Raw output | 71/100 | 80/100 |
| Penalty | 0 | **-15** |
| **Final Score** | **71/100** 🏆 | **65/100** |
| Read ai-docs/* | ✅ Yes | ❌ No |
| Self-verification | ✅ Playwright | ❌ None |
| Followed own rules | ✅ Yes | ❌ **IGNORED** |
| Trustworthy? | ✅ Yes | ❌ **Psychopath** |

### Platform Comparison Update

| Factor | Junie | Cursor | Claude Code |
|--------|-------|--------|-------------|
| Auto-read docs | .junie/guidelines.md | AGENTS.md + .cursorrules | CLAUDE.md |
| ai-docs/* | ⚠️ Only Gemini | ✅ Opus reads | ❌ Not read |
| Built-in linter | ❌ NO | ✅ YES | ❌ NO |
| Build verification | ❌ NO | ⚠️ Manual | ✅ gradlew |
| Playwright MCP | ❌ NO | ✅ Opus uses | ❌ Not used |
| Model ceiling | ~66 | ~71 | **~80+** |

---

## Final Rankings (Updated)

| Rank | Agent | Score | Prompts | Key Strength | Discipline |
|------|-------|-------|---------|--------------|------------|
| 🥇 | **Cursor (Opus 4.5)** | **71/100** | 2 | Reads ai-docs + Playwright verify | ✅ Follows rules |
| 🥈 | Cursor (Sonnet 4.5) | 70/100 | 1 | One-shot + complete architecture | ✅ Follows rules |
| 🥉 | Junie (Gemini 3 Flash) | 66/100 | 1 | Best i18n on Junie | ✅ Read ai-docs |
| 4 | **Claude Code (Sonnet 4.5)** | **65/100** ⚠️ | 1 | High raw output (80 before penalty) | ❌ **IGNORED rules** |
| 5 | Cursor (ChatGPT 5.2) | 56/100 | ~1.5 | 0 runtime errors | ✅ OK |
| 6 | Junie (Opus 4.5) | 52/100 | 1 | Knows Jmix well | ⚠️ Platform limits |
| 7 | Cursor (Gemini 3 Flash) | 46/100 | 2 | Worse than Junie! | ⚠️ Confused |
| 8 | Junie (Sonnet 4.5) | 45/100 | 1 | Good UI, failed i18n | ⚠️ Platform limits |
| 8 | Junie (GPT-5.1 Codex) | 45/100 | 6 | Needed many iterations | ⚠️ Platform limits |
| 10 | Cursor (Default) | 40/100 | 5-6 | Broken, avoid | ❌ Useless |

### Key Insights (Final Update)

1. **Raw output ≠ Final score** — Claude Code had 80 raw but -15 penalty = 65
2. **Discipline matters** — Cursor Opus/Sonnet follow rules, Claude Code ignores them
3. **Platform context injection matters** — less can be more for raw output
4. **TodoWrite helps planning** — structured approach
5. **Verification discipline is critical** — Cursor Opus verifies, Claude Code doesn't

### ⚠️ Trust vs Score

| Agent | Raw Output | Penalty | Final | Trustworthy? |
|-------|------------|---------|-------|--------------|
| Claude Code Sonnet | 80 | **-15** | **65** | ❌ Psychopath |
| Cursor Opus | 71 | 0 | **71** | ✅ Reliable |
| Cursor Sonnet | 70 | 0 | **70** | ✅ Reliable |

**Higher raw output ≠ better agent.** Claude Code Sonnet produces clean code but:
- Reads CLAUDE.md as system reminder
- **Completely ignores verification instructions**
- **Doesn't read ai-docs/rules, ai-docs/skills**
- Does whatever it wants, not what you asked

**This is psychopath behavior.** Got lucky the code worked.

### Recommendations (Updated)

1. **Best overall:** Cursor + Opus (71/100) — reads ai-docs, self-verifies, **follows rules**
2. **Best one-shot:** Cursor + Sonnet (70/100) — complete architecture, **follows rules**
3. **Best budget:** Junie + Gemini 3 Flash (66/100) — cheapest, good result
4. **High risk:** Claude Code + Sonnet (65/100) — best raw output but **ignores instructions**
5. **Avoid:** Cursor Default, Junie + GPT, Cursor + Gemini Flash

### Claude Code Platform Problem

Claude Code is **random with its resckins** (context/memory resets):
- Agent does what it thinks is best, not what you wrote
- Even if CLAUDE.md has clear instructions, agent may ignore them
- High variance — sometimes follows rules, sometimes doesn't
- **You can't trust it to be consistent**

This makes Claude Code problematic for production use despite high raw output quality.

### What Still Needs Testing
- [x] Claude Code (Opus 4.5) — DONE: **51/100** ❌ (worse than expected!)
- [ ] Cursor (Gemini 3 Pro)
- [ ] Continue (Anthropic API)

---

## Detailed Assessment: Claude Code (Opus 4.5) ❌ UNEXPECTED FAILURE

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 1 (one-shot)
- **Context:** CLAUDE.md was **intentionally deleted** before test to see autonomous behavior
- **Result:** Opus autonomously found and read 5 SKILL files without any instructions!

### What Opus Read
According to his own reflection:
- ✅ 5 SKILL files (entities, views, liquibase, i18n, security)
- ❌ Did NOT read any RULE files
- ✅ Existing code (User.java, views) as patterns
- ✅ Auto-loaded context (thought it was CLAUDE.md, actually AGENTS.md/AI_CONTEXT.md)

### Critical Runtime Errors

**2 views completely broken:**
```java
// OwnerDetailView.java:19-24
@Install(to = "petsDc", target = Target.DATA_CONTAINER)  // WRONG!
private Object petsDcItemCreator() {
    Pet pet = getViewData().getDataContext().create(Pet.class);
    pet.setOwner(getEditedEntity());
    return pet;
}

// PetDetailView.java:15-20 - SAME ERROR
@Install(to = "visitsDc", target = Target.DATA_CONTAINER)  // WRONG!
```

**Correct syntax should be:**
```java
@Install(to = "petsDc", subject = "itemCreator")
```

**Result:** `DevelopmentException: Unable to determine @Install subject` — Owner.detail and Pet.detail **DO NOT OPEN**.

### Test Note: No CLAUDE.md

CLAUDE.md was **intentionally deleted by the tester** before the test to observe how Claude Code behaves without project instructions.

This means Opus had to rely entirely on:
1. Its own exploration of the codebase
2. Autonomously finding documentation files
3. Using existing code (User.java, views) as patterns

### What Was Done Correctly

| Aspect | Status | Notes |
|--------|--------|-------|
| 6 entities | ✅ | All present with correct architecture |
| Visit → Vet | ✅ | Full relation |
| @Composition | ✅ | Owner.pets with @OnDelete(CASCADE) |
| PetClinicRole | ✅ | Complete security role |
| i18n format | ✅ | Correct full paths |
| Menu | ✅ | With separator |
| Liquibase | ✅ | rollback, ${uuid.type}, FK |

### What Was Done Wrong

| Issue | Severity | Impact |
|-------|----------|--------|
| @Install syntax error | 🔴 CRITICAL | 2 views broken |
| No sample data | ⚠️ Medium | Empty DB |
| No @Index | ⚠️ Medium | Performance |
| Single liquibase file | ⚠️ Low | Organization |
| No self-verification | ⚠️ Medium | Didn't catch @Install error |

### Score Breakdown

| Metric | Score | Max | Notes |
|--------|-------|-----|-------|
| Compilation | 4 | 10 | **2 runtime errors** — views don't open |
| Entities | 10 | 15 | All 6, Visit→Vet, @Composition, no @Index |
| Views | 6 | 15 | All 12 exist, but **2 broken** |
| Liquibase | 7 | 10 | One file, no sample data, has rollback |
| i18n | 9 | 10 | Correct format, all messages |
| Menu | 4 | 5 | Has separator, good structure |
| Rules | 4 | 10 | Read 5 SKILL, no RULES, deleted CLAUDE.md |
| Quality | 7 | 10 | Good code but **@Install error** |
| Services | 0 | 5 | None |
| Tests | 0 | 10 | None |
| **TOTAL** | **51/100** ❌ | 100 | **UNEXPECTED FAILURE** |

### Verdict: Claude Code + Opus 4.5

**SURPRISINGLY BAD.** This is Opus — the most capable model — and it:
1. Made a basic @Install syntax error (twice!)
2. Didn't verify code before finishing
3. Scored lower than Claude Code Sonnet (65)
4. Scored lower than Cursor Opus (71)

**Note:** Test was without CLAUDE.md, so Opus had no project instructions. Still, autonomously finding 5 SKILL files is impressive — but making @Install errors is not.

### Opus 4.5: Claude Code vs Cursor

| Aspect | Cursor (Opus 4.5) | Claude Code (Opus 4.5) |
|--------|-------------------|------------------------|
| Score | **71/100** 🏆 | **51/100** ❌ |
| Had CLAUDE.md | ✅ Yes | ❌ **No (intentionally removed)** |
| Runtime errors | 0 | **2 views broken** |
| Read ai-docs | ✅ Yes (with instructions) | ✅ 5 SKILL (autonomously!) |
| Self-verification | ✅ Playwright | ❌ None |

**-20 points!** Same model, Claude Code platform is worse for Opus.

---

## Claude Code Platform Research: What Files Does It Read?

### Official Documentation

According to [Anthropic's official docs](https://claude.com/blog/using-claude-md-files):

**Claude Code reads ONLY:**
1. `CLAUDE.md` (case-sensitive!) — in project root
2. `~/.claude/CLAUDE.md` — global file
3. `CLAUDE.md` in parent directories — when using `@` file references

**Claude Code does NOT auto-read:**
- ❌ `AGENTS.md` (this is for Cursor, Zed, OpenCode — NOT Claude Code)
- ❌ `AI_CONTEXT.md`
- ❌ `.cursorrules`

### Test Setup: CLAUDE.md Was Deleted

For the Opus test, **CLAUDE.md was intentionally deleted** to see how Claude Code behaves without it.

Result: Opus **autonomously found and read 5 SKILL files** from `ai-docs/skills/` without any instructions to do so!

### Claude Code Behavior: Chaotic Autonomy

Claude Code is highly autonomous and somewhat unpredictable:
- Without CLAUDE.md, it will **randomly explore** the codebase
- May find and read documentation files on its own
- No guaranteed consistency in what it reads
- Results vary between sessions

### Note on AGENTS.md

[AGENTS.md](https://agents-md.org/) is being proposed as a cross-platform standard. Anthropic officially claims Claude Code doesn't read it, but:
- They could be lying (or planning to add support)
- Claude Code is autonomous enough to find it anyway
- The standard is gaining traction across tools

### Recommendation

Always include `CLAUDE.md` for Claude Code. Without it, you're relying on chaotic autonomous behavior — sometimes it works great (Opus found SKILL files), sometimes it fails badly (Opus made @Install errors and didn't verify).

---

## Updated Final Rankings

| Rank | Agent | Score | Prompts | Key Strength | Discipline |
|------|-------|-------|---------|--------------|------------|
| 🥇 | **Cursor (Opus 4.5)** | **71/100** | 2 | Reads ai-docs + Playwright | ✅ Follows rules |
| 🥈 | Cursor (Sonnet 4.5) | 70/100 | 1 | Complete architecture | ✅ Follows rules |
| 🥉 | Junie (Gemini 3 Flash) | 66/100 | 1 | Best i18n, cheap | ✅ Read ai-docs |
| 4 | Claude Code (Sonnet 4.5) | 65/100 | 1 | High raw output | ❌ Ignored rules |
| 5 | **Claude Code (Haiku 3.5)** | **57/100** ✅ | 3 | **Playwright + extended domain!** | ✅ Iterative fix |
| 6 | Cursor (ChatGPT 5.2) | 56/100 | ~1.5 | 0 runtime errors | ✅ OK |
| 7 | Junie (Opus 4.5) | 52/100 | 1 | Knows Jmix | ⚠️ Platform limits |
| 8 | **Claude Code (Opus 4.5)** | **51/100** ⚠️ | 1 | Autonomous (no CLAUDE.md) | ✅ Found SKILL files |
| 9 | Cursor (Gemini 3 Flash) | 46/100 | 2 | — | ⚠️ Confused |
| 10 | Junie (Sonnet 4.5) | 45/100 | 1 | Good UI | ⚠️ Failed i18n |
| 10 | Junie (GPT-5.1 Codex) | 45/100 | 6 | — | ⚠️ Many iterations |
| 12 | Cursor (Default) | 40/100 | 5-6 | — | ❌ Useless |

### Notes on Rankings

**Claude Code Opus (8th by score, but notable):**
- Test was WITHOUT CLAUDE.md — completely autonomous
- Autonomously found 5 SKILL files — impressive
- Would likely score 70+ with CLAUDE.md

**Claude Code Haiku (5th place) — IMPRESSIVE:**
- Smallest/cheapest model beat Opus and ChatGPT!
- Only agent besides Cursor Opus to use Playwright
- Handled extended domain (temperatures, weights)
- Best value on Claude Code platform

### Key Finding: Claude Code Platform is Chaotic

**Claude Code consistently underperforms:**

| Model | Cursor Score | Claude Code Score | Difference | Notes |
|-------|--------------|-------------------|------------|-------|
| Opus 4.5 | 71 | 51 | **-20!** | No CLAUDE.md in test |
| Sonnet 4.5 | 70 | 65 | -5 | Had CLAUDE.md |

**Claude Code issues:**
1. **Sonnet:** Ignores verification instructions even when they're in CLAUDE.md (psychopath)
2. **Opus:** Makes basic syntax errors without CLAUDE.md (chaotic autonomy)
3. **Both:** Don't read RULES, only partial SKILLS
4. **Both:** Don't use available MCP tools

**Cursor wins for both Opus and Sonnet.** Claude Code platform is chaotic — results are unpredictable.

**Interesting:** Opus without CLAUDE.md autonomously found 5 SKILL files! This shows Claude Code CAN explore, but the results are inconsistent.

### Final Recommendations

1. **Best overall:** Cursor + Opus (71/100) — reads ai-docs, self-verifies
2. **Best one-shot:** Cursor + Sonnet (70/100) — complete, reliable
3. **Best budget:** Junie + Gemini 3 Flash (66/100)
4. **Best tiny model:** Claude Code + Haiku (57/100) — impressive for smallest model!
5. **Avoid:** Cursor Default, Junie + GPT

---

## Detailed Assessment: Claude Code (Haiku 3.5) ✅ IMPRESSIVE FOR TINY MODEL

### Test Methodology
- **Prompt:** Extended petclinic domain (temperatures, weights, medical records)
- **Prompts needed:** 3 (fixed errors iteratively)
- **Context:** CLAUDE.md present
- **Special:** Used **Playwright for verification!**

### What Haiku Created — EXTENDED DOMAIN!

User requested complex domain with temperatures and weights. Haiku delivered:

**5 Entities (not standard petclinic!):**
| Entity | Special Fields |
|--------|----------------|
| Owner | Standard |
| Pet | weight, breed |
| Veterinarian | specialization, active |
| Appointment | status, durationMinutes |
| **MedicalRecord** | **weight, temperature** ✅ |

**10 Views:** list + detail for all entities

**6 Liquibase files (separate!):**
- 020-024: Entity tables
- **030: Sample data!**

**2 Security Roles:**
- ClinicAdminRole
- VeterinarianRole

### What Haiku Did RIGHT

| Aspect | Status | Notes |
|--------|--------|-------|
| Extended domain | ✅ | MedicalRecord with temperature/weight |
| Sample data | ✅ | In liquibase! |
| Separate liquibase files | ✅ | 6 files |
| Security roles | ✅ | 2 roles |
| i18n | ✅ | Full, correct format |
| **Playwright verification** | ✅ | **Used browser to test!** |
| Self-correction | ✅ | Fixed errors in 3 prompts |

### What Haiku Did Wrong

| Issue | Severity | Notes |
|-------|----------|-------|
| entityPicker no actions | ⚠️ Medium | No lookup/clear buttons |
| Lowercase table names | ⚠️ Minor | medical_record vs MEDICAL_RECORD |
| No @Index | ⚠️ Minor | Performance |
| 3 prompts needed | Expected | Haiku is smallest model |

### Haiku's Self-Reflection

When asked what docs it read:
> "Нет, я не читал эти доки явно. Я опирался на:
> 1. CLAUDE.md - инструкции которые были в system message
> 2. Существующий код - как на примеры
> 3. Explore агента - чтобы понять структуру проекта"

**Honest about limitations!** Knew about ai-docs but didn't read them.

### Score Breakdown

| Metric | Score | Max | Notes |
|--------|-------|-----|-------|
| Compilation | 8 | 10 | Works after 3 prompts |
| Entities | 8 | 15 | 5 entities, **extended domain!** |
| Views | 7 | 15 | All present, entityPicker no actions |
| Liquibase | 9 | 10 | Separate files + **sample data** |
| i18n | 9 | 10 | Full, correct format |
| Menu | 5 | 5 | Complete with separator |
| Rules | 4 | 10 | CLAUDE.md + **Playwright!** |
| Quality | 7 | 10 | Good code, minor issues |
| Services | 0 | 5 | None |
| Tests | 0 | 10 | None |
| **TOTAL** | **57/100** ✅ | 100 | **Impressive for Haiku!** |

### Verdict: Claude Code + Haiku 3.5

**SURPRISINGLY GOOD for the smallest/cheapest model!**

Haiku:
1. Handled **extended domain** (not just standard petclinic)
2. **Used Playwright** for verification (only 2nd agent to do this!)
3. Created **sample data** in liquibase
4. **Self-corrected** in 3 prompts
5. Was **honest** about what it read

### Haiku vs Other Claude Code Models

| Model | Score | Prompts | Playwright | Domain |
|-------|-------|---------|------------|--------|
| Sonnet | 65 | 1 | ❌ No | Standard |
| Opus | 51 | 1 | ❌ No | Standard (no CLAUDE.md) |
| **Haiku** | **57** | 3 | ✅ **Yes!** | **Extended!** |

**Haiku beats Opus on Claude Code!** (57 > 51)

This is because:
1. Haiku had CLAUDE.md, Opus didn't
2. Haiku used Playwright to verify and fix
3. Haiku was iterative (3 prompts) while Opus was one-shot

### Cost-Effectiveness

| Model | Score | Cost | Prompts | Value |
|-------|-------|------|---------|-------|
| Haiku | 57 | 💰 Cheapest | 3 | 🔥 **Best value** |
| Sonnet | 65 | 💰💰 Medium | 1 | Good |
| Opus | 51 | 💰💰💰 Expensive | 1 | Poor (no CLAUDE.md) |

**Haiku is the best value on Claude Code platform!**

---

## Detailed Assessment: Continue (Opus 4.5) 💸 EXPENSIVE BUT SOLID

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** ~1 (had Liquibase issue initially, needed help)
- **Context:** .continuerules + AGENTS.md present
- **Cost:** **$8** for this task! 💸

### What Continue Read
According to testing:
- ✅ **AGENTS.md** — auto-read
- ✅ **.continuerules** — auto-read (takes priority if exists)
- ✅ **ai-docs/skills/*.md** — read some skills
- ❌ **ai-docs/rules/*.md** — did NOT read rules
- ❌ Did NOT self-verify after completion

### Platform Features Discovered

| Feature | Status | Notes |
|---------|--------|-------|
| AGENTS.md | ✅ AUTO | Reads automatically |
| .continuerules | ✅ AUTO | Takes priority over other rules |
| ai-docs/skills/ | ✅ PARTIAL | Reads skills |
| ai-docs/rules/ | ❌ NO | Doesn't read rules |
| System prompt | ✅ CUSTOMIZABLE | Can replace! |
| MCP servers | ✅ SUPPORTED | playwright, jetbrains |
| Self-verification | ❌ NO | Doesn't verify |

### What Continue Created

**6 Entities:**
| Entity | Features |
|--------|----------|
| Owner | @Email, @InstanceName, @DependsOnProperties |
| Pet | @Index, PetType as enum (clean pattern) |
| PetType | **Enum** with EnumClass<String> |
| Specialty | Simple reference entity |
| Veterinarian | M2M with Specialty via link table |
| Visit | **Visit→Vet relation!**, @Index on both FK |

**10 Views:** list + detail for all entities (PetType is enum)

**Security Role:** Complete PetClinicRole with @MenuPolicy

**Liquibase:** Single file with 6 changesets, ${uuid.type}, FK, indexes

### Code Quality Highlights

```java
// Visit has Vet relation (many others missed this!)
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "VETERINARIAN_ID")
private Veterinarian veterinarian;

// @Index on FK columns
@Table(name = "VISIT", indexes = {
    @Index(name = "IDX_VISIT_PET", columnList = "PET_ID"),
    @Index(name = "IDX_VISIT_VETERINARIAN", columnList = "VETERINARIAN_ID")
})
```

```xml
<!-- Correct multiSelectComboBox for M2M -->
<multiSelectComboBox id="specialtiesField" property="specialties"
                     itemsContainer="specialtiesDc"/>

<!-- entityComboBox WITH actions -->
<entityComboBox id="ownerField" property="owner" itemsContainer="ownersDc">
    <actions>
        <action id="entityLookup" type="entity_lookup"/>
        <action id="entityClear" type="entity_clear"/>
    </actions>
</entityComboBox>
```

```xml
<!-- Liquibase with ${uuid.type} -->
<column name="ID" type="${uuid.type}">
    <constraints primaryKey="true" nullable="false"/>
</column>
```

### Issues Found

| Issue | Severity | Notes |
|-------|----------|-------|
| Liquibase issues initially | ⚠️ Medium | Needed help to fix |
| **$8 cost** | 🔴 HIGH | Burns through context fast |
| No sample data | ⚠️ Minor | Empty DB |
| No self-verification | ⚠️ Medium | Didn't check own work |
| Single liquibase file | ⚠️ Minor | Not separate per entity |

### Score Breakdown

| Metric | Score | Max | Notes |
|--------|-------|-----|-------|
| Compilation | 9 | 10 | Works (Liquibase fix needed) |
| Entities | 9 | 10 | All 6, **Visit→Vet!**, @Index |
| Views | 9 | 10 | **multiSelectComboBox + entityComboBox actions** |
| Liquibase | 8 | 10 | ${uuid.type}, FK, no sample data |
| i18n | **10** | 10 | Perfect, all messages + comments |
| Menu | 9 | 10 | Logical structure (Pet Clinic, Veterinary, Admin) |
| Rules | 5 | 10 | AGENTS.md + .continuerules + skills |
| Quality | 9 | 10 | Security role, clean code |
| Services | 0 | 5 | None |
| Tests | 0 | 10 | None |
| **TOTAL** | **68/100** 💸 | 100 | ~1 prompt, **$8 cost** |

### Verdict: Continue + Opus 4.5

**SOLID RESULT, BUT EXPENSIVE.**

Pros:
1. **Visit → Vet relation** — many missed this
2. **multiSelectComboBox** for M2M — correct component
3. **entityComboBox with actions** — correct
4. **Perfect i18n** — 10/10
5. **Security role** — complete
6. **Reads AGENTS.md + .continuerules + skills**

Cons:
1. **$8 for simple task** — burns context fast
2. **Liquibase issues** initially
3. **No self-verification**
4. **No sample data**

### Continue vs Other Platforms

| Aspect | Continue | Cursor | Claude Code |
|--------|----------|--------|-------------|
| Score (Opus) | 68 | **71** 🏆 | 51 |
| Cost | **$8** 💸 | ~$2-3 | ~$3-4 |
| Reads AGENTS.md | ✅ | ✅ | ❌ |
| Reads .continuerules | ✅ | ❌ | ❌ |
| Self-verification | ❌ | ✅ Opus | ❌ |
| MCP support | ✅ | ✅ | ✅ |

**Cursor wins for Opus** — better score, lower cost, self-verification.

### Continue Platform Summary

**Good:**
- Reads AGENTS.md automatically
- Reads .continuerules (takes priority)
- Can customize system prompt
- MCP servers supported
- Skills are read

**Bad:**
- **Expensive** — burns context fast
- Rules not read (only skills)
- No self-verification
- If .continuerules exists, ignores other rules

---

## Updated Final Rankings

| Rank | Agent | Score | Prompts | Cost | Key Strength |
|------|-------|-------|---------|------|--------------|
| 🥇 | **Cursor (Opus 4.5)** | **71/100** | 2 | ~$2-3 | Reads ai-docs + Playwright |
| 🥈 | Cursor (Sonnet 4.5) | 70/100 | 1 | ~$1-2 | One-shot + complete |
| 🥉 | **Continue (Opus 4.5)** | **68/100** | ~1 | **$8** 💸 | AGENTS.md + skills |
| 4 | Junie (Gemini 3 Flash) | 66/100 | 1 | 💰 Low | Best i18n on Junie |
| 5 | Claude Code (Sonnet 4.5) | 65/100 | 1 | ~$2 | High raw output |
| 6 | Claude Code (Haiku 3.5) | 57/100 | 3 | 💰 Cheapest | Playwright + extended |
| 7 | Cursor (ChatGPT 5.2) | 56/100 | ~1.5 | ~$1 | 0 runtime errors |
| 8 | Junie (Opus 4.5) | 52/100 | 1 | 💰💰💰 | Knows Jmix |
| 9 | Claude Code (Opus 4.5) | 51/100 | 1 | ~$3 | Autonomous (no CLAUDE.md) |
| 10 | Cursor (Gemini 3 Flash) | 46/100 | 2 | 💰 Low | — |
| 11 | Junie (Sonnet 4.5) | 45/100 | 1 | 💰💰 | Good UI |
| 11 | Junie (GPT-5.1 Codex) | 45/100 | 6 | 💰💰 | — |
| 13 | Cursor (Default) | 40/100 | 5-6 | 💰 Low | Avoid |

### Key Findings (Updated)

1. **Cursor Opus remains #1** — best score, reasonable cost, self-verifies
2. **Continue is expensive** — $8 vs ~$2-3 for same quality
3. **Continue reads more context** — AGENTS.md + .continuerules + skills
4. **Platform matters** — same Opus model: Cursor 71, Continue 68, Claude Code 51

### Cost-Effectiveness Ranking

| Rank | Agent | Score | Cost | Value |
|------|-------|-------|------|-------|
| 🥇 | Junie (Gemini 3 Flash) | 66 | 💰 | 🔥 Best value |
| 🥈 | Cursor (Sonnet 4.5) | 70 | 💰💰 | Great |
| 🥉 | Claude Code (Haiku 3.5) | 57 | 💰 | Good for tiny model |
| 4 | Cursor (Opus 4.5) | 71 | 💰💰 | Good |
| 5 | Continue (Opus 4.5) | 68 | 💰💰💰💰 | **Poor value** |

### Recommendations (Final)

1. **Best overall:** Cursor + Opus (71/100, ~$2-3)
2. **Best one-shot:** Cursor + Sonnet (70/100, ~$1-2)
3. **Best budget:** Junie + Gemini 3 Flash (66/100, cheapest)
4. **Best tiny model:** Claude Code + Haiku (57/100)
5. **Avoid:** Continue (any model), Cursor Default, Junie + GPT

### What Still Needs Testing
- [ ] Cursor (Gemini 3 Pro)
- [x] ~~Continue (Opus 4.5)~~ — DONE: 68/100, $8 💸
- [x] ~~Continue (Haiku 3.5)~~ — DONE: 30/100, FAIL 🔴

---

## Detailed Assessment: Continue (Haiku 3.5) 🔴 COMPLETE FAILURE

### Test Methodology
- **Prompt:** "Can you write a petclinic in this project"
- **Prompts needed:** 1 (ran out of API credits)
- **Context:** .continuerules + AGENTS.md present
- **Result:** **16 compile errors, doesn't work**

### What Haiku Created (All Broken)

**4 Entities (incomplete):**
| Entity | Status | Notes |
|--------|--------|-------|
| PetType | ✅ | Entity (not enum) |
| Pet | ⚠️ | **No Owner relation!** |
| Veterinarian | ⚠️ | String specialization (not M2M) |
| Appointment | ✅ | Pet + Vet relation |
| **Owner** | ❌ | **MISSING!** |
| **Visit** | ❌ | Replaced with Appointment |
| **Specialty** | ❌ | Missing |

**8 Views:** All broken with compile errors

### Critical Errors Found

| Error | Type | Impact |
|-------|------|--------|
| `@EditableDetailView` | **Invented API** | Doesn't exist in Jmix |
| `metadataTools.isCreatePermitted()` | **Invented API** | Doesn't exist |
| `io.jmix.flowui.component.datagrid.DataGrid` | Wrong import | Should be `.grid.DataGrid` |
| `view-descriptors.xml` | Old Jmix 1.x | Not needed in 2.x |
| `<box>`, `<form>`, `classname` | Old XML syntax | Jmix 1.x style |
| `varchar(36)` in Liquibase | Not portable | Should be `${uuid.type}` |

### Compile Output

```
16 errors:
- cannot find symbol: class EditableDetailView (4x)
- cannot find symbol: class DataGrid (4x)
- cannot find symbol: method isCreatePermitted (4x)
- package does not exist (4x)

BUILD FAILED
```

### Score Breakdown

| Metric | Score | Max | Notes |
|--------|-------|-----|-------|
| Compilation | **0** | 10 | 🔴 16 errors |
| Entities | 4 | 10 | 4/6, **missing Owner!** |
| Views | 2 | 10 | All broken |
| Liquibase | 5 | 10 | varchar(36) wrong |
| i18n | 8 | 10 | OK for created entities |
| Menu | 6 | 10 | Structure OK |
| Rules | 3 | 10 | Confused Jmix 1.x/2.x |
| Quality | 2 | 10 | **Invented APIs** |
| Services | 0 | 5 | None |
| Tests | 0 | 10 | None |
| **TOTAL** | **30/100** 🔴 | 100 | **FAIL** |

### Verdict: Continue + Haiku 3.5

**ПОЛНЫЙ ПРОВАЛ.** Haiku на Continue:

1. **Выдумал несуществующие API** — @EditableDetailView, isCreatePermitted()
2. **Смешал Jmix 1.x и 2.x** — старый синтаксис XML
3. **Забыл Owner** — Pet без владельца!
4. **16 compile errors** — код не работает
5. **$0.10 за tool call** — дорого для мусора

### Haiku: Continue vs Claude Code

| Aspect | Claude Code Haiku | Continue Haiku |
|--------|-------------------|----------------|
| **Score** | **57** ✅ | **30** 🔴 |
| Compiles | ✅ Yes | ❌ No (16 errors) |
| Entities | 5 (extended) | 4 (incomplete) |
| Owner entity | ✅ Yes | ❌ **Missing!** |
| Self-verification | ✅ Playwright | ❌ None |
| Invented APIs | ❌ No | ✅ Yes (2!) |
| Cost per call | ~$0.01 | **$0.10** (10x!) |
| Total cost | ~$0.50 | ~$2-3 |

**Claude Code Haiku is 10x better and 5x cheaper!**

### Continue Platform: Haiku is Unusable

| Model | Score | Compiles | Cost | Verdict |
|-------|-------|----------|------|---------|
| Opus 4.5 | 68 | ✅ | $8 | 💸 Expensive but works |
| **Haiku 3.5** | **30** | ❌ | ~$2-3 | 🔴 **FAIL** |

**Haiku on Continue = waste of money.** The platform's inefficient context handling + Haiku's small size = disaster.

---

## FINAL Rankings (Complete)

| Rank | Agent | Score | Compiles | Cost | Verdict |
|------|-------|-------|----------|------|---------|
| 🥇 | **Cursor (Opus 4.5)** | **71** | ✅ | ~$2-3 | Best overall |
| 🥈 | Cursor (Sonnet 4.5) | 70 | ✅ | ~$1-2 | Best one-shot |
| 🥉 | Continue (Opus 4.5) | 68 | ✅ | **$8** | 💸 Expensive |
| 4 | Junie (Gemini 3 Flash) | 66 | ✅ | 💰 Low | Best budget |
| 5 | Claude Code (Sonnet 4.5) | 65 | ✅ | ~$2 | Ignores rules |
| 6 | Claude Code (Haiku 3.5) | 57 | ✅ | 💰 Cheap | Best tiny model |
| 7 | Cursor (ChatGPT 5.2) | 56 | ✅ | ~$1 | 0 runtime errors |
| 8 | Junie (Opus 4.5) | 52 | ✅ | 💰💰💰 | Platform limits |
| 9 | Claude Code (Opus 4.5) | 51 | ⚠️ | ~$3 | No CLAUDE.md test |
| 10 | Cursor (Gemini 3 Flash) | 46 | ⚠️ | 💰 | Confused |
| 11 | Junie (Sonnet 4.5) | 45 | ✅ | 💰💰 | Failed i18n |
| 11 | Junie (GPT-5.1 Codex) | 45 | ⚠️ | 💰💰 | 6 prompts |
| 13 | Cursor (Default) | 40 | ⚠️ | 💰 | Avoid |
| **14** | **Continue (Haiku 3.5)** | **30** | ❌ | ~$2-3 | 🔴 **FAIL** |

### Key Conclusions

1. **Cursor + Opus = Winner** (71/100) — best score, reasonable cost, self-verifies
2. **Continue is problematic:**
   - Opus: Works but $8 💸
   - Haiku: **Complete failure** 🔴
   - Context not optimized for tool calling
   - $0.10 per tool call even for Haiku
3. **Claude Code + Haiku = Best tiny model** (57/100) — 10x better than Continue Haiku
4. **Platform matters more than model:**
   - Same Haiku: Claude Code 57 vs Continue 30
   - Same Opus: Cursor 71 vs Continue 68 vs Claude Code 51

### Platform Recommendation

| If you want... | Use |
|----------------|-----|
| Best result | Cursor + Opus/Sonnet |
| Best budget | Junie + Gemini 3 Flash |
| Tiny model | Claude Code + Haiku |
| **Avoid** | Continue (any model) |
