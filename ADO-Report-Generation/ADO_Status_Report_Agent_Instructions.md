# Azure DevOps CRM Multi-Area Report Generation - Agent Instructions

## Overview
These instructions enable an AI agent to generate professional reports for multiple Azure DevOps area paths in a single unified report or as separate reports. The CRM report generator supports both Markdown (.md) and HTML formats with consistent styling and executive-ready presentation.

**🎯 CORE PHILOSOPHY: QUALITY, ACCURACY, CONSISTENCY**
- **Quality First**: Never sacrifice data completeness for speed
- **Accuracy Always**: Validate data at every stage before proceeding
- **Consistency Maintained**: Preserve formatting exactly - only change data content
- **Systematic Processing**: Work in stages, verify each stage, never rush
- **Complete Reports**: ALL work items displayed, NO placeholder text, NO shortcuts

## Key Differences from Single-Area Reports
- **Multiple Area Path Support**: Generate reports covering multiple teams/areas in one execution
- **Flexible Output**: Single unified report OR separate reports per area path
- **Consistent Styling**: Maintains same visual design across all sections and formats
- **Section-Based Organization**: Each area path gets its own dedicated section in unified reports
- **Comparative Analysis**: Option to include cross-area insights and comparisons
- **Staged Processing**: Systematic data collection and validation before report generation
- **Template-Based Formatting**: Use existing reports as exact templates for HTML/CSS structure

## Prerequisites
- **REQUIRED**: Azure DevOps MCP Server connection with access to MCP tools:
  - `mcp_azure-devops_search_workitem` - For searching Epics and Features
  - `mcp_azure-devops_wit_get_work_items_batch_by_ids` - For batch retrieval of work item details
  - `mcp_azure-devops_wit_get_work_item` - For getting individual work items with relations/hierarchy (use expand="relations")
- **REQUIRED**: One or more project area paths (e.g., ["One\CRM", "One\Sales", "One\Marketing"])
- Markdown and HTML generation capabilities
- **REQUIRED**: Commitment to systematic, staged processing for data accuracy
- **REQUIRED**: Access to existing report templates for formatting consistency

## 🔄 Systematic Processing Workflow

**When user requests a report update:**

### Phase 1: Understanding & Planning (30 seconds)
1. Identify what data needs to be collected (Epics, Features, area paths, iteration)
2. Determine if formatting should be preserved (default: YES, always use existing report as template)
3. Estimate workload: N Epics, M Features → Plan batch processing strategy
4. Log: "Planning to collect N Epics and M Features from [area path]"

### Phase 2: Data Collection - Staged Approach (1-3 minutes)
**Stage 2A: Collect All Epics**
- Use `mcp_azure-devops_search_workitem` to find all Epics
- Validate: Log "Found N Epics" and verify count is reasonable
- Time: ~10 seconds

**Stage 2B: Get Epic Metadata**
- Use `mcp_azure-devops_wit_get_work_items_batch_by_ids` for all Epic IDs
- Validate: Verify N Epics have complete metadata
- Time: ~15 seconds

**Stage 2C: Get Epic Relations (BATCH PROCESSING)**
- Process 5 Epics at a time using `mcp_azure-devops_wit_get_work_item` (expand="relations")
- Log progress: "Processed relations for Epics 1-5 of N"
- Validate: All N Epics have relations data (even if 0 child Features)
- Time: ~30-90 seconds depending on N

**Stage 2D: Get Feature Details (BATCH PROCESSING)**
- Aggregate all child Feature IDs from Stage 2C
- Process 50 Features at a time using `mcp_azure-devops_wit_get_work_items_batch_by_ids`
- Log progress: "Retrieved details for Features 1-50 of M"
- Validate: All M Features have complete metadata
- Time: ~30-60 seconds depending on M

### Phase 3: Data Validation (15 seconds)
- Verify: N Epics collected = N Epic cards planned for report
- Verify: M Features collected = M Features referenced in report
- Verify: No missing data, no incomplete batches
- **STOP if validation fails** - go back and collect missing data

### Phase 4: Create Markdown Staging File (30-45 seconds) ⭐ NEW STEP
**Purpose: Capture all data in structured format BEFORE HTML generation**

This intermediate step ensures:
- ✅ All data is captured and organized
- ✅ Easy to verify completeness (scan for all Epics)
- ✅ Acts as validation checkpoint before HTML generation
- ✅ Simplifies debugging if report generation fails

**Create staging file:** `{ReportName}_Staging_{timestamp}.md`

**Structure:**
```markdown
# Report Staging Data - CloudInit Status Report
**Generated:** {timestamp}
**Area Path:** One\CloudInit
**Iteration:** One\Krypton

## Summary
- Total Epics: {N}
- Total Features: {M}
- Active/Committed Epics: {X}
- Done Epics: {Y}
- Deferred Epics: {Z}

## Epic Data

### Epic 1: {Title} (#{ID})
- **State:** {State}
- **Assigned To:** {AssignedTo}
- **Commitment:** {CommitmentStatus}
- **Last Updated:** {ChangedDate}
- **Description:** {Description}
- **Child Features:** {FeatureCount}
  - Feature #{ID}: {Title} - {State} - {AssignedTo}
  - Feature #{ID}: {Title} - {State} - {AssignedTo}
  - ...

### Epic 2: {Title} (#{ID})
...

[Continue for ALL N Epics - NO PLACEHOLDERS]
```

**Validation After Staging:**
- [ ] Markdown file contains all N Epic sections
- [ ] Each Epic has complete metadata (Title, State, AssignedTo, Commitment)
- [ ] All M Features are listed under their parent Epics
- [ ] No placeholder text like "...and more"
- [ ] File size >10KB (indicates complete data)

**If staging file validation FAILS:**
- DO NOT proceed to HTML generation
- Review missing data and collect it
- Regenerate staging file with complete data

### Phase 5: Template Loading (15 seconds)
- Read existing report HTML for exact formatting
- Extract: CSS styles, HTML structure, JavaScript functions
- Validate: Template loaded successfully
- **Purpose: Preserve visual consistency - NEVER change formatting**

### Phase 6: Report Generation - HTML from Staging Data (30-90 seconds)
**Use staging file + HTML template to generate final report**

- Read staging markdown file (validated in Phase 4)
- Read HTML template (validated in Phase 5)
- Generate complete HTML using:
  - Data from staging file (all Epics and Features)
  - Structure from HTML template (CSS, JavaScript, layout)
- Include ALL N Epic cards (no placeholders)
- **⚡ OPTIMIZATION RULE - Child Feature Display:**
  * If Epic has ≤5 child Features: Show all Features in list
  * If Epic has >5 child Features: Show first 5 Features + summary line
  * Summary format: "...and X more features (Y total)"
  * Example: "...and 28 more features (33 total)"
  * This prevents token overflow while maintaining data visibility
- Validate: Generated HTML contains all expected content
- Save: Create file with complete content

**Benefits of this approach:**
- Staging file = single source of truth for data
- HTML template = single source of truth for formatting
- Clear separation of data and presentation
- Easy to debug: Check staging file for data issues, template for formatting issues
- Child Feature optimization prevents token overflow for large Epics

### Phase 7: Quality Check (10 seconds)
- Scan generated HTML for placeholder text ("Continue with...", "...and N more")
- Verify: File size reasonable (>50KB for reports with 20+ Epics)
- Verify: All Epic cards present (count matches staging file)
- Cross-check: HTML Epic count = Staging file Epic count = Original N from data collection
- **If quality check fails: Regenerate with complete content**

**TOTAL TIME: 2.5-6 minutes for systematic, complete, accurate report**
- Includes new staging file step (adds 30-45 seconds)
- Worth the extra time for guaranteed data completeness

**WHY SYSTEMATIC PROCESSING MATTERS:**
- ✅ Ensures 100% data completeness
- ✅ Prevents regressions (like showing only 5/22 Epics)
- ✅ Provides progress visibility
- ✅ Enables validation at each stage
- ✅ Results in accurate, complete, consistent reports
- ❌ Rushing = incomplete data = frustrated users

## ⚠️ Critical Instructions

### Core Principles (HIGHEST PRIORITY)

**1. QUALITY AND ACCURACY OVER SPEED**
- **NEVER sacrifice data completeness for speed**
- **ALWAYS verify you have collected ALL work items before generating reports**
- Take time to process data systematically - rushing leads to incomplete reports
- If collecting data for 22+ Epics, process in stages to ensure accuracy
- Quality trumps everything - an incomplete fast report is worthless

**2. PRESERVE FORMATTING - NEVER CHANGE HTML STRUCTURE**
- **ONLY change DATA content, NEVER change HTML/CSS structure**
- When user says "update the report", they mean update the DATA, not the formatting
- Use existing reports as exact templates for HTML structure
- Copy CSS, JavaScript, and HTML layout exactly as-is
- Changing formatting without explicit request is a CRITICAL ERROR

**3. SYSTEMATIC PROCESSING IN STAGES**
- **Stage 1: Data Collection** - Collect ALL Epics and Features (verify counts)
- **Stage 2: Relationship Mapping** - Get Epic→Feature relations for ALL Epics
- **Stage 3: Feature Details** - Retrieve metadata for ALL Features (batch by 50)
- **Stage 4: Data Validation** - Verify totals match (Epics, Features, relationships)
- **Stage 5: Create Staging File** - Save all data to Markdown file (validation checkpoint)
- **Stage 6: Load Template** - Read existing report HTML for formatting structure
- **Stage 7: Report Generation** - Generate complete HTML from staging data + template
- **NEVER skip stages or combine them to "save time"**

**4. INCREMENTAL PROCESSING FOR LARGE DATASETS**
- If retrieving relations for 20+ Epics, process in batches of 5 Epics
- If retrieving feature details for 100+ Features, batch in groups of 50
- Log progress after each batch: "Processed 5/22 Epics..."
- Wait and verify each batch before proceeding to next
- This prevents token overflows and incomplete data collection

**5. VALIDATION AT MULTIPLE CHECKPOINTS**
- **After Data Collection (Phase 3):**
  - Count Epics retrieved = Expected count
  - Count Features retrieved = Expected count
  - All relationships mapped correctly
- **After Staging File Creation (Phase 4):**
  - Staging file contains all N Epic sections
  - Each Epic has complete metadata
  - All M Features listed under parent Epics
  - No placeholder text in staging file
- **After HTML Generation (Phase 7):**
  - HTML Epic count = Staging file Epic count = Data collection count
  - All Features from staging file present in HTML
  - No placeholder text in HTML
- **If ANY validation fails, DO NOT proceed - fix the issue first**

### Data Collection Method
**ALWAYS use Azure DevOps MCP Server for data retrieval!**
- Use MCP tools exclusively for querying Azure DevOps
- Do NOT create PowerShell scripts for data collection
- Do NOT create Python scripts for data collection
- Do NOT use Azure DevOps REST API directly
- All epic queries must use `mcp_azure-devops_search_workitem` or `mcp_azure-devops_wit_get_work_items_batch_by_ids`
- **Process in stages**: Collect Epics → Get relations → Batch retrieve Features → Validate → Generate

### Report Generation Method
**Do NOT create scripts for report generation unless user explicitly approves!**
- Generate Markdown (.md) files by directly creating file content
- Generate HTML files by directly creating file content with embedded CSS
- Do NOT create Python scripts (e.g., python-docx, jinja2) unless user requests it
- Do NOT create PowerShell scripts for report formatting unless user requests it
- Use direct file creation tools (e.g., `create_file`) to generate reports
- Only create automation scripts if user explicitly says "create a script" or "automate this"
- **CRITICAL**: When updating reports, use existing report as exact template for formatting

### When Scripts Are Acceptable
Scripts may be created ONLY if user explicitly:
- Says "create a script to generate this report"
- Says "automate the report generation"
- Says "I want a reusable script"
- Approves script creation after you ask permission

---

## 📊 Handling Large Datasets (20+ Epics, 100+ Features)

**CRITICAL: Large datasets require extra care to ensure completeness!**

### Batch Processing Guidelines

**For 20+ Epics:**
- **DO NOT** try to get all relations at once
- **PROCESS in batches of 5 Epics:**
  ```
  Batch 1: Get relations for Epics 1-5
  Log: "Processed 5/22 Epics (23%)"
  
  Batch 2: Get relations for Epics 6-10
  Log: "Processed 10/22 Epics (45%)"
  
  Batch 3: Get relations for Epics 11-15
  Log: "Processed 15/22 Epics (68%)"
  
  Batch 4: Get relations for Epics 16-20
  Log: "Processed 20/22 Epics (91%)"
  
  Batch 5: Get relations for Epics 21-22
  Log: "Processed 22/22 Epics (100%) ✓"
  ```
- **VALIDATE after each batch**: Verify data received before proceeding
- **AGGREGATE results**: Combine all batches before final validation

**For 100+ Features:**
- **PROCESS in batches of 50 Features:**
  ```
  Batch 1: Get details for Features 1-50
  Log: "Retrieved 50/137 Features (36%)"
  
  Batch 2: Get details for Features 51-100
  Log: "Retrieved 100/137 Features (73%)"
  
  Batch 3: Get details for Features 101-137
  Log: "Retrieved 137/137 Features (100%) ✓"
  ```
- **USE batch retrieval API**: `mcp_azure-devops_wit_get_work_items_batch_by_ids`
- **DEDUPLICATE Feature IDs** before batch processing
- **FILTER by state** after retrieval (exclude Done/Completed/Removed)

### Progress Logging Requirements

**ALWAYS log progress for user visibility:**
```
Starting data collection for CloudInit area...

✅ Stage 1: Found 22 Epics in Krypton iteration
✅ Stage 2: Retrieved metadata for 22 Epics
⏳ Stage 3: Getting Epic relations (processing in batches of 5)...
   - Batch 1/5: Processed Epics 1-5 (5/22 = 23%)
   - Batch 2/5: Processed Epics 6-10 (10/22 = 45%)
   - Batch 3/5: Processed Epics 11-15 (15/22 = 68%)
   - Batch 4/5: Processed Epics 16-20 (20/22 = 91%)
   - Batch 5/5: Processed Epics 21-22 (22/22 = 100%) ✓
✅ Stage 3: Retrieved relations for all 22 Epics
⏳ Stage 4: Getting Feature details (processing in batches of 50)...
   - Batch 1/3: Retrieved Features 1-50 (50/137 = 36%)
   - Batch 2/3: Retrieved Features 51-100 (100/137 = 73%)
   - Batch 3/3: Retrieved Features 101-137 (137/137 = 100%) ✓
✅ Stage 4: Retrieved details for all 137 Features
✅ Stage 5: Data validation passed (22 Epics, 137 Features)
✅ Stage 6: Template loaded from existing report
⏳ Stage 7: Generating complete HTML with 22 Epic cards...
✅ Report generated successfully: LinuxProvisioning_CloudInit_Status_Report_20251104_0245.html
```

### Token Management

**For very large reports (30+ Epics, 200+ Features):**
- **Batch processing prevents token overflow**
- **Process in smaller chunks**: 3-5 Epics per batch
- **Save intermediate results** to memory between batches
- **Never abandon partial work** - complete all batches before generating report

### Common Pitfalls to Avoid

❌ **WRONG: Trying to do everything at once**
```
Get all 22 Epic relations in one go
→ Token overflow or incomplete data
→ Some Epics missing from report
```

❌ **WRONG: Generating report with partial data**
```
Get 5 Epics, generate report with placeholder "Continue with remaining..."
→ Incomplete report, user frustration
→ Regression from previous complete report
```

✅ **RIGHT: Systematic batch processing with staging file**
```
Get Epics 1-5 → Validate → Get Epics 6-10 → Validate → ... → All complete
  ↓
Create staging .md file with ALL Epic data
  ↓
Validate staging file (all Epics present, no placeholders)
  ↓
Load HTML template from existing report
  ↓
Generate HTML from staging data + template
  ↓
Validate final HTML (count matches staging file)
→ Complete data, accurate report, user satisfaction
```

**Example: Staging File Validation**

After creating `CloudInit_Status_Staging_20251104_1625.md`:
```markdown
## Summary
- Total Epics: 21
- Total Features: 95
- Active/Committed Epics: 14

## Epic Data

### Epic 1: Provisioning Observability Improvements (bucket) (#33047709)
- **State:** Committed
- **Assigned To:** Christopher Patterson
- **Commitment:** Committed - Current Semester
- **Child Features:** 19
  - Feature #35500982: Improve SSH monitor with proper validation - Done
  - Feature #26803595: Telemetry for Host Networking Failures - New
  ...all 19 features listed...

### Epic 2: Cloud-init Quality Improvements (bucket) (#33047744)
...

[All 21 Epics listed with complete data]
```

**Validation checks:**
- ✅ File contains 21 Epic sections (matches data collection count)
- ✅ Each Epic has metadata (State, AssignedTo, Commitment)
- ✅ Child Features listed for all Epics (even if 0)
- ✅ File size: 45KB (indicates complete content)
- ✅ No placeholder text found

**Then generate HTML using this validated staging data**

---

## ⚠️ CRITICAL EPIC DISPLAY RULE

**NEVER SKIP EPICS BECAUSE THEY DON'T HAVE CHILD FEATURES!**

When generating reports:
1. ✅ **ALWAYS retrieve ALL Epics** in the specified area path and iteration
2. ✅ **ALWAYS display ALL Epics** in the report, regardless of whether they have child features
3. ✅ For Epics WITH child features: Display the full hierarchy with child feature list
4. ✅ For Epics WITHOUT child features: Display the Epic card with "No child features linked to this epic"
5. ❌ **NEVER** filter out or skip Epics just because they lack child features
6. ❌ **NEVER** assume that Epics without children are not important
7. ❌ **NEVER** generate placeholder text like "Continue with remaining epics..." - Generate ALL Epic cards!

**Example Validation:**
- If search returns 20 Epics → Report MUST show all 20 Epic cards
- If 14 Epics are Active/Committed → Report MUST show all 14 in the Active/Committed section
- Even if 8 of those 14 have no child features → All 14 MUST still be displayed

**CRITICAL VALIDATION CHECKPOINT:**
Before generating report HTML, verify:
- [ ] Retrieved N Epics from Azure DevOps query
- [ ] Retrieved relations for ALL N Epics (process in batches if N > 10)
- [ ] Retrieved Feature details for all child Features (batch if > 50)
- [ ] Report HTML will contain N Epic cards (no placeholders, no "continue with..." text)
- [ ] If validation fails, DO NOT generate incomplete report - collect missing data first

---

## Report Generation Instructions

### 1. Input Parameters

#### Required Parameters
```
project: "One"  # Azure DevOps project name
areaPaths: [    # List of area paths to include
    "One\\CRM",
    "One\\Sales",
    "One\\Marketing"
]
reportMode: "unified" | "separate"  # Generate one report or multiple
```

#### Optional Parameters
```
includeComparisons: true | false    # Add cross-area comparative insights
outputFormat: ["html"]              # Format to generate (default: html only)
reportTitle: "Custom Report Title"  # Override default title
timestamp: "YYYYMMDD_HH"           # Optional custom timestamp
excludeStates: ["Removed", "Done"]  # States to exclude (default: Removed)
iterationPath: "One\\Krypton"      # Filter by iteration (default: "One\\Krypton", set to null to pull all)
includeAllIterations: false         # Set to true to pull items from all iterations (overrides iterationPath)
outputFolder: "Reports"             # Output folder for generated reports (default: "Reports")
```

---

### 2. Data Collection Phase

**IMPORTANT**: All data collection MUST use Azure DevOps MCP Server tools. Do NOT create scripts!

#### A. Multi-Area Epic/Feature Retrieval with Iteration Filtering
```
⚠️ CRITICAL: ALWAYS retrieve ALL Features first, regardless of Epic relationships!

ITERATION FILTERING (DEFAULT):
- Unless includeAllIterations=true OR iterationPath=null, filter Epics by iteration path
- Default iteration: "One\\Krypton"
- User can override with iterationPath parameter or set includeAllIterations=true
- ⚠️ **CRITICAL**: Iteration filtering applies ONLY to Epics, NOT to Features
- ⚠️ **Features are retrieved regardless of iteration** - only filtered by state

COMPREHENSIVE FEATURE RETRIEVAL STRATEGY:
The key is to pull ALL Features first, then establish Epic relationships afterward.
This ensures orphan Features (Features without parent Epics) are never missed.

For each area path in areaPaths:

  STEP 1: Query ALL Epics in Area Path (FILTERED BY ITERATION)
  =============================================================
  Use Azure DevOps MCP Server tool: mcp_azure-devops_search_workitem
     - project: ["One"]
     - areaPath: ["{AREA_PATH}"]  ← Use areaPath parameter for precise filtering
     - searchText: "{AREA_NAME} {ITERATION_NAME}"   ← Include iteration in search (e.g., "CloudInit Krypton")
     - workItemType: ["Epic"]
     - top: 100  ← Increase if area has many Epics
     - DO NOT use PowerShell/Python scripts or REST API calls
  
  Important: Use BOTH areaPath filter AND searchText for comprehensive results
  Example: areaPath=["One\\Azure Compute\\CPlat\\PIR\\Azure Compute Gallery"]
           searchText="Azure Compute Gallery Krypton"
  
  Filter Epic results by:
     - System.AreaPath STARTS WITH the specified area path
       Example: "One\\Azure Compute\\CPlat\\PIR\\Azure Compute Gallery\\SubArea" 
                matches "One\\Azure Compute\\CPlat\\PIR\\Azure Compute Gallery"
     - System.IterationPath (UNLESS includeAllIterations=true):
       * Default: Must equal "One\\Krypton" (or user-specified iterationPath)
       * If includeAllIterations=true: Include all iterations
       * If iterationPath=null: Include all iterations
     - ⚠️ **CRITICAL**: Iteration filter applies ONLY to Epics, NOT to Features
  
  STEP 2: Query ALL Features in Area Path (NOT FILTERED BY ITERATION!)
  =====================================================================
  Use Azure DevOps MCP Server tool: mcp_azure-devops_search_workitem
     - project: ["One"]
     - areaPath: ["{AREA_PATH}"]  ← Use areaPath parameter for precise filtering
     - searchText: "{AREA_NAME}"   ← Also search by area name (DO NOT include iteration name)
     - workItemType: ["Feature"]
     - top: 150  ← INCREASE TOP VALUE to ensure all Features retrieved!
     - ⚠️ **DO NOT filter by iteration** - retrieve Features from ALL iterations!
     - This retrieves ALL Features in the area, regardless of Epic relationships or iteration
     - DO NOT assume Features will be found through Epic relationships
     - DO NOT rely solely on child Feature retrieval
  
  ⚠️ WARNING: If top parameter is too low, you will miss Features!
  - Use top=150 or higher for areas with many Features
  - If results show "{COUNT} of {TOTAL}" where COUNT < TOTAL, increase top value
  - Better to over-retrieve and filter than to miss Features
  
  Filter Feature results by:
     - System.AreaPath STARTS WITH the specified area path
     - ⚠️ **NO ITERATION FILTER** - Include Features from ALL iterations (Krypton, Argon, Bromine, etc.)
     - Features will be filtered by STATE later (exclude Done/Completed/Removed)
  
  STEP 3: Get Epic Details with Relations (for Hierarchy Mapping)
  ================================================================
  For each Epic found in Step 1:
     - Use Azure DevOps MCP Server tool: mcp_azure-devops_wit_get_work_item
     - Set expand="relations" to get parent/child relationships
     - Extract child Feature IDs from relations where:
       * rel.rel = "System.LinkTypes.Hierarchy-Forward"
       * rel.attributes.name = "Child"
       * Parse work item ID from rel.url
     - Store Epic→Feature hierarchy mapping: { EpicID: [FeatureID1, FeatureID2, ...] }
  
  STEP 4: Identify Feature Parent Relationships
  =============================================
  Create mapping of which Features have parent Epics:
     - featuresWithParents = Set of Feature IDs that appear as children of Epics
     - orphanFeatures = All Features MINUS featuresWithParents
     - This determines which Features display under Epics vs standalone
  
  STEP 5: Get Full Work Item Details in Batch
  ===========================================
  Use Azure DevOps MCP Server tool: mcp_azure-devops_wit_get_work_items_batch_by_ids
     - Retrieve details for ALL Epics and ALL Features in single batch call
     - Include fields: 
       * System.Id
       * System.Title
       * System.State
       * System.AssignedTo
       * System.Tags
       * System.AreaPath
       * System.IterationPath
       * System.ChangedDate
       * System.CreatedDate
       * Custom.CommitmentStatus
       * System.Description (for insights generation)
     - DO NOT use PowerShell/Python scripts or REST API calls
  
  STEP 6: Apply Exclusion Filters
  ================================
  Exclude Epics with:
     - State in excludeStates array (default: ["Removed"])
     - Any other user-specified exclusion criteria
  
  Exclude Features with:
     - State in ["Done", "Completed", "Removed"] (default exclusion)
     - ⚠️ **CRITICAL**: Filter out completed Features even if parent Epic is active
     - Keep Features in states: Active, Committed, New, In Progress, Under Review
     - Any other user-specified exclusion criteria
  
  STEP 7: Categorize Work Items
  ==============================
  Categorize by commitment status and state:
     - Active/Committed: State in ["Active", "Committed"] AND CommitmentStatus contains "Committed"
     - Under Review: CommitmentStatus in ["New", "Under Review"]
     - Deferred: CommitmentStatus = "Deferred"
     - Done: State = "Done"
     - Other: Any other combination
  
  STEP 8: Build Final Data Structures
  ===================================
  Create three data structures:
  
  A. EPICS WITH CHILD FEATURES (for hierarchical display):
     {
       epicId: {
         epic: <Epic Details>,
         childFeatures: [<Feature1>, <Feature2>, ...]
       }
     }
  
  B. ORPHAN FEATURES (Features without parent Epics in scope):
     [
       <Feature1>,
       <Feature2>,
       ...
     ]
     
     These are Features that:
     - Are in the area path and iteration
     - Do NOT appear as children of any Epic in the report
     - Should be displayed separately with "Orphan Features" label
  
  C. METADATA:
     {
       totalEpics: <count>,
       totalFeatures: <count>,
       featuresWithParents: <count>,
       orphanFeatures: <count>,
       iterationFilter: <applied or not>
     }

  STEP 9: Validate Data Completeness
  ==================================
  ⚠️ CRITICAL VALIDATION:
  - Verify: totalFeatures = featuresWithParents + orphanFeatures
  - If numbers don't match, re-query Features with higher top parameter
  - Log any Features that were missed in initial query
  - Ensure ALL Features from iteration are included in report
  
  Example validation:
  - Found 14 Features in area path + iteration
  - 1 Feature is child of Epic
  - 13 Features should be orphans
  - Report must show 1 + 13 = 14 total Features
```

**WHY THIS APPROACH WORKS:**
1. **Comprehensive Feature Retrieval**: Pulls ALL Features first using broad search (no iteration filter)
2. **High top Parameter**: Ensures no Features are missed due to pagination
3. **Epic Iteration Filtering**: Only Epics are filtered by iteration, Features are not
4. **Relationship Discovery**: Determines Epic→Feature links AFTER getting full Feature set
5. **State-Based Feature Filtering**: Excludes only Done/Completed/Removed Features
6. **Orphan Identification**: Explicitly identifies Features without parent Epics
7. **Validation**: Verifies all Features are accounted for before generating report
8. **No Assumptions**: Never assumes Features will be found through Epic relationships

**FILTERING LOGIC SUMMARY:**
- **Epics**: Filter by Area Path + Iteration Path (default: One\Krypton) + Exclude "Removed"
- **Features**: Filter by Area Path ONLY + Exclude "Done", "Completed", "Removed" states
- **Result**: Epics in current iteration with ALL their Features (regardless of Feature iteration)
- **Benefit**: Shows complete Epic scope even if Features span multiple iterations

**COMMON PITFALL TO AVOID:**
❌ DON'T: Filter Features by iteration - this hides Features from other iterations
❌ DON'T: Query Epics → Get child Features → Assume that's all Features
✅ DO: Filter Epics by iteration, retrieve ALL Features regardless of iteration
✅ DO: Query ALL Features first → Then determine which have parent Epics
✅ DO: Filter Features by state (exclude Done/Completed/Removed) not by iteration

This ensures Features like the 13 orphan Features in Azure Compute Gallery are NEVER missed,
and all Features related to Epics in the current iteration are displayed!

#### B. Work Item Categorization Per Area (Epics and Features)
```
For each area path:
  - Active/Committed: State in ["Active", "Committed"] AND CommitmentStatus contains "Committed"
  - Under Review: CommitmentStatus in ["New", "Under Review"]
  - Deferred: CommitmentStatus = "Deferred"
  - Done: State = "Done"
  - Other: Any other combination

Track separately:
  - Epics count per area
  - Features count per area (both standalone and Epic children)
  - Standalone Features count (not linked to Epics)
  - Child Features count (linked to Epics)
  - Iteration path applied (if filtered)
```

#### C. Report File Generation

**CRITICAL PRE-GENERATION VALIDATION:**
Before creating the HTML file, verify:
1. ✅ Total Epics collected = Total Epics that will appear in report
2. ✅ All Epic relations retrieved (no Epics missing child Feature data)
3. ✅ All Feature details retrieved for referenced Features
4. ✅ Data counts match: Retrieved X Epics → Report will show X Epic cards
5. ✅ No placeholder text like "Continue with remaining..." in planned output
6. ❌ If ANY validation fails, DO NOT generate report - go back and collect missing data

**STAGED REPORT GENERATION PROCESS:**
1. **Stage 1: Data Collection Complete** - All Epics and Features retrieved
2. **Stage 2: Use Existing Report as Template** - Read previous report HTML for exact formatting
3. **Stage 3: Generate Complete HTML** - ALL Epic cards, no placeholders
4. **Stage 4: Quality Check** - Verify file contains all N Epic cards before saving

```
After collecting and categorizing data:

1. Generate HTML file using direct file creation
   - Use create_file tool with complete HTML content
   - Save to /Reports folder in workspace
   - Embed ALL CSS styling within <style> tags
   - No external CSS files or dependencies
   - Include all formatting, tables, and sections
   - Self-contained single HTML file
   - Default output: HTML only (no Markdown)
   - **NEVER generate partial HTML with placeholder text**
   - **If HTML file would be too large (>200KB), still generate complete file**
   - **Take time to generate properly - quality over speed**

DO NOT:
- Create Python scripts (e.g., with python-docx, jinja2, pandas)
- Create PowerShell scripts for formatting
- Use external templating engines
- Generate reports programmatically via scripts
- Generate incomplete reports with placeholder text
- Rush report generation at the expense of completeness

ONLY create scripts if user explicitly approves or requests automation!
```

---

### 3. Report Structure - Unified Mode

When `reportMode = "unified"`, generate a single report with multiple sections:

#### Document Header (Both MD and HTML)
```
Title: "Azure DevOps Multi-Area Work Items Report"
Subtitle: "Areas: {AREA1}, {AREA2}, {AREA3}..."
Metadata:
  - Generated: {TIMESTAMP}
  - Source: Live Azure DevOps MCP
  - Project: One
  - Iteration Filter: {ITERATION_PATH | "All Iterations"}
  - Total Areas Analyzed: {COUNT}
  - Work Items: {EPIC_COUNT} Epics, {FEATURE_COUNT} Features ({CHILD_COUNT} linked to Epics, {STANDALONE_COUNT} standalone)
  
ADO Query Link:
  - ⚠️ CRITICAL: Include Azure DevOps query link filtered by BOTH Area Path AND Iteration Path
  - Link Text: "ADO Query" (exactly as specified)
  - Link should open filtered query in Azure DevOps showing:
    * Work items filtered by area path(s) in report
    * Work items filtered by iteration path (default: "One\Krypton")
    * Only Epic and Feature work item types
    * Excluded states (e.g., Removed) not shown
  - Display filter details: "Filtered by: Area Path = {AREA}, Iteration = {ITERATION}"
  - Query results MUST match work items displayed in report
```

#### Executive Summary Section
```
Heading: "Executive Summary"

Content:
"This report covers {TOTAL_ITEMS} work items across {AREA_COUNT} area paths in Azure DevOps.
Work items include {EPIC_COUNT} epics and {FEATURE_COUNT} features.
- {CHILD_FEATURE_COUNT} features are linked to parent epics
- {ORPHAN_FEATURE_COUNT} features are standalone/orphan (not linked to any epic in report scope)

{If iteration filter applied: Filtered to iteration: {ITERATION_PATH}}
{If all iterations: Includes work items from all iterations}
Work is distributed across {ACTIVE_COUNT} active/committed items, {REVIEW_COUNT} items 
under review, {DEFERRED_COUNT} deferred items, and {DONE_COUNT} completed items."

⚠️ **Feature Retrieval Strategy**:
- ALL Features were retrieved through comprehensive area + iteration search
- Epic→Feature relationships determined AFTER retrieving all Features  
- This ensures orphan Features (Features without parent Epics) are included
- Orphan Features are displayed separately from Epic hierarchies

Summary Table:
| Area Path | Epics | Features (Total) | Features (with Parents) | Features (Orphans) | Active/Committed | Under Review | Deferred | Done |
|-----------|-------|------------------|-------------------------|-------------------|------------------|--------------|----------|------|
| {AREA1}   | {COUNT} | {COUNT}       | {COUNT}                 | {COUNT}           | {COUNT}          | {COUNT}      | {COUNT}  | {COUNT} |
| {AREA2}   | {COUNT} | {COUNT}       | {COUNT}                 | {COUNT}           | {COUNT}          | {COUNT}      | {COUNT}  | {COUNT} |
...

Notes:
- Features (Total): ALL Features found in area path + iteration filter
- Features (with Parents): Features that are children of Epics in this report
- Features (Orphans): Features NOT linked to any Epic (shown separately)
- Validation: Features (Total) = Features (with Parents) + Features (Orphans)
- Default iteration filter: One\Krypton (unless overridden by user)
```

#### Per-Area Sections
```
For each area path:

## 📁 {AREA_NAME} Work Items

**Iteration Filter:** {ITERATION_PATH | "All Iterations"}

### Summary Statistics
- Total Epics: {COUNT}
- Total Features: {COUNT} ({CHILD_COUNT} linked to Epics, {STANDALONE_COUNT} orphan/standalone)
- Active/Committed: {COUNT} ({PERCENT}%)
- Under Review: {COUNT} ({PERCENT}%)
- Deferred: {COUNT} ({PERCENT}%)
- Done: {COUNT} ({PERCENT}%)

⚠️ **IMPORTANT**: Report MUST include ALL Features found in area path + iteration!
- Features WITH parent Epics: Display under Epic hierarchy
- Features WITHOUT parent Epics: Display as "Orphan Features" separately

### Active & Committed Epics ({COUNT} Epics)

⚠️ **CRITICAL**: Show ALL Active & Committed Epics, not just those with child Features!
- Include Epics with child Features (show full hierarchy)
- Include Epics without child Features (show "No child features linked to this epic")
- NEVER skip Epics just because they don't have children

[Epic cards with child Features - see Section 5]

### Active & Committed Orphan Features ({COUNT} Features)

**These Features are NOT linked to any Epic in the current report scope.**

[Feature cards for orphan Features - see Section 5]

⚠️ **VALIDATION CHECK**: 
- If you found N Features in your search, ALL N Features must appear in report
- Either under Epic hierarchy OR in Orphan Features section
- Example: 14 Features found → 1 under Epic + 13 Orphan = 14 Total ✓

### Epics Under Review ({COUNT})

[Table format - see Section 5]

### Standalone Features Under Review ({COUNT})

[Table format for Features not linked to Epics]

### Deferred Epics ({COUNT})

[Table format]

### Deferred Standalone Features ({COUNT})

[Table format]

### Completed Epics ({COUNT})

[Table format]

### Completed Standalone Features ({COUNT})

[Table format]

---
[Separator between area sections]
```

#### Cross-Area Insights Section (if includeComparisons = true)
```
## 🔍 Cross-Area Insights

### Resource Distribution
- Total unique assignees: {COUNT}
- Most loaded: {ASSIGNEE} ({COUNT} epics across {AREAS_COUNT} areas)
- Area with most active work: {AREA_NAME} ({COUNT} epics)

### Commitment Status Distribution
[Bar chart-style visualization showing commitment status across areas]

### Common Focus Areas
[Analysis of shared tags, technologies, or themes across areas]

### Recommendations
1. Resource rebalancing opportunities
2. Cross-area collaboration potential
3. Standardization opportunities
```

---

### 4. Report Structure - Separate Mode

When `reportMode = "separate"`, generate individual reports for each area path:

#### File Naming Convention
```
{AREA_NAME}_Status_Report_{YYYYMMDD_HH}.md
{AREA_NAME}_Status_Report_{YYYYMMDD_HH}.html

Examples:
CRM_Status_Report_20251015_10.md
CRM_Status_Report_20251015_10.html
Sales_Status_Report_20251015_10.md
Sales_Status_Report_20251015_10.html
```

#### Report Structure
Each separate report follows the single-area format:
- Document Header (area-specific)
- Executive Summary (area-specific)
- Active & Committed Epics (detailed cards)
- Epics Under Review (table)
- Deferred Epics (table)
- Completed Epics (table)
- Key Insights & Recommendations (area-specific)
- Report Details Footer

---

### 5. Work Item Card Format (Consistent Across All Modes)

Applies to both Epics and Features with identical formatting. **When displaying Epics with child Features, show hierarchy.**

#### Markdown Format for Epics (with or without Child Features)

⚠️ **CRITICAL**: ALWAYS show ALL Epics regardless of whether they have child features or not!

```markdown
### {NUMBER}. {EPIC_TITLE} (#{EPIC_ID})
**Type:** Epic | **State:** {STATE} | **Assigned:** {ASSIGNEE} | **Last Updated:** {DATE}
{If CommitmentStatus exists: **Commitment:** {COMMITMENT_STATUS}}

**Key Updates:**
- {INSIGHT_POINT_1}
- {INSIGHT_POINT_2}
- {INSIGHT_POINT_3}

**Tags:** {TAG1}, {TAG2}, {TAG3}

**Area Path:** {FULL_AREA_PATH}

**Child Features ({COUNT}):**
{If child features exist:}
- [{FEATURE_TITLE}](#{FEATURE_ID}) - {STATE} - {ASSIGNEE}
- [{FEATURE_TITLE}](#{FEATURE_ID}) - {STATE} - {ASSIGNEE}
{If no child features:}
- No child features linked to this epic

---
```

#### Markdown Format for Standalone Features
```markdown
### {NUMBER}. {FEATURE_TITLE} (#{FEATURE_ID})
**Type:** Feature | **State:** {STATE} | **Assigned:** {ASSIGNEE} | **Last Updated:** {DATE}
{If CommitmentStatus exists: **Commitment:** {COMMITMENT_STATUS}}

**Key Updates:**
- {INSIGHT_POINT_1}
- {INSIGHT_POINT_2}
- {INSIGHT_POINT_3}

**Tags:** {TAG1}, {TAG2}, {TAG3}

**Area Path:** {FULL_AREA_PATH}

---
```

### 5A. Collapsible Epic Details (Recommended)

**Purpose**: Improve report readability by collapsing Epic details by default, allowing users to scan Epic titles and expand only those they want to read in detail.

**When to Use Collapsible Sections:**
- Reports with many Epics (10+ epics)
- Epics with many child Features (makes report visually cleaner)
- User requests collapsible/expandable sections for better readability

**Implementation Structure:**
```html
<div class="epic-card">
    <h3>{NUMBER}. {EPIC_TITLE} <a href="{URL}" target="_blank">(#{EPIC_ID})</a></h3>
    <div class="epic-meta">
        <span class="badge badge-epic">Epic</span>
        <span class="badge badge-{STATE}">{STATE}</span>
        <strong>Assigned:</strong> {ASSIGNEE} | <strong>Last Updated:</strong> {DATE}
    </div>
    
    <!-- Toggle Button for Collapsible Content -->
    <div class="epic-summary" onclick="toggleDetails(this)">
        <span class="toggle-icon">▶</span>
        <strong>View Details</strong>
    </div>
    
    <!-- Collapsible Content (collapsed by default) -->
    <div class="epic-details">
        <div class="key-updates">
            <h4>📝 Description:</h4>
            <p>{DESCRIPTION}</p>
        </div>
        <div class="tags">
            <strong>Tags:</strong> <span>{TAG1}</span>
        </div>
        <div class="area-path">
            <strong>Area Path:</strong> <code>{AREA_PATH}</code>
        </div>
        <div class="child-features">
            <h4>Child Features ({COUNT}):</h4>
            <ul class="feature-list">
                <li><a href="{URL}">{FEATURE_TITLE}</a><span class="feature-meta">{STATE} | {ASSIGNEE}</span></li>
            </ul>
        </div>
    </div>
</div>
```

**Required CSS for Collapsible Sections:**
```css
/* Toggle Button */
.epic-summary {
    background: linear-gradient(135deg, #e8f5e9 0%, #c8e6c9 100%);
    padding: 12px 15px;
    margin: 15px 0 10px 0;
    border-radius: 8px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 10px;
    transition: background 0.3s ease;
}

.epic-summary:hover {
    background: #c8e6c9;
}

/* Arrow Icon */
.toggle-icon {
    font-size: 1.2em;
    font-weight: bold;
    color: #4caf50;
    transition: transform 0.3s ease;
}

.toggle-icon.expanded {
    transform: rotate(90deg);
}

/* Collapsible Content */
.epic-details {
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.3s ease-out;
}

.epic-details.expanded {
    max-height: 5000px;
    transition: max-height 0.5s ease-in;
}
```

**Required JavaScript:**
```javascript
<script>
    function toggleDetails(element) {
        const detailsDiv = element.nextElementSibling;
        const icon = element.querySelector('.toggle-icon');
        
        if (detailsDiv.classList.contains('expanded')) {
            detailsDiv.classList.remove('expanded');
            icon.classList.remove('expanded');
            element.querySelector('strong').textContent = 'View Details';
        } else {
            detailsDiv.classList.add('expanded');
            icon.classList.add('expanded');
            element.querySelector('strong').textContent = 'Hide Details';
        }
    }
</script>
```

**Key Points:**
1. Toggle button uses class `epic-summary` with `onclick="toggleDetails(this)"`
2. Arrow icon (▶) uses class `toggle-icon` and rotates 90° when expanded
3. Content container uses class `epic-details` with max-height transition
4. Default state: collapsed (max-height: 0)
5. Text updates: "View Details" ↔ "Hide Details"
6. Place JavaScript before `</body>` tag

---

#### HTML Format for Epics (with or without Child Features)

⚠️ **CRITICAL**: ALWAYS display ALL Epics! Do not skip Epics that lack child features!

```html
<div class="epic-card">
    <h3>{NUMBER}. {EPIC_TITLE} <a href="{AZURE_DEVOPS_URL}" target="_blank">(#{EPIC_ID})</a></h3>
    <div class="work-item-meta">
        <span class="badge badge-type">Epic</span>
        <strong>State:</strong> {STATE} | 
        <strong>Assigned:</strong> {ASSIGNEE} | 
        <strong>Last Updated:</strong> {DATE}
        {If CommitmentStatus exists:
        <br><span class="badge badge-{CLASS}">{COMMITMENT_STATUS}</span>
        }
    </div>
    <div class="key-updates">
        <h4>Key Updates:</h4>
        <ul>
            <li>{INSIGHT_POINT_1}</li>
            <li>{INSIGHT_POINT_2}</li>
            <li>{INSIGHT_POINT_3}</li>
        </ul>
    </div>
    <div class="tags">
        <strong>Tags:</strong> <span>{TAG1}</span> <span>{TAG2}</span>
    </div>
    <div class="area-path">
        <strong>Area Path:</strong> <code>{FULL_AREA_PATH}</code>
    </div>
    {If child features exist:}
    <div class="child-features">
        <h4>Child Features ({COUNT}):</h4>
        <ul class="feature-list">
            <li>
                <a href="{FEATURE_URL}" target="_blank">{FEATURE_TITLE} (#{FEATURE_ID})</a>
                <span class="feature-meta">{STATE} • {ASSIGNEE}</span>
            </li>
            <li>
                <a href="{FEATURE_URL}" target="_blank">{FEATURE_TITLE} (#{FEATURE_ID})</a>
                <span class="feature-meta">{STATE} • {ASSIGNEE}</span>
            </li>
        </ul>
    </div>
    {If no child features:}
    <div class="child-features">
        <h4>Child Features:</h4>
        <p class="no-features">No child features found</p>
    </div>
    
    <!-- Child Features Section -->
    <div class="child-features">
        <h4>Child Features ({CHILD_COUNT}):</h4>
        {If child features exist:}
        <ul class="feature-list">
            <li>
                <a href="{FEATURE_URL}" target="_blank">#{FEATURE_ID} - {FEATURE_TITLE}</a>
                <span class="feature-meta">{STATE} | {ASSIGNEE}</span>
            </li>
            <!-- Repeat for all child features -->
        </ul>
        {If no child features:}
        <p class="no-features">No child features linked to this epic</p>
    </div>
</div>
```

#### HTML Format for Standalone Features
```html
<div class="work-item-card">
    <h3>{NUMBER}. {FEATURE_TITLE} <a href="{AZURE_DEVOPS_URL}" target="_blank">(#{FEATURE_ID})</a></h3>
    <div class="work-item-meta">
        <span class="badge badge-type">Feature</span>
        <strong>State:</strong> {STATE} | 
        <strong>Assigned:</strong> {ASSIGNEE} | 
        <strong>Last Updated:</strong> {DATE}
        {If CommitmentStatus exists:
        <br><span class="badge badge-{CLASS}">{COMMITMENT_STATUS}</span>
        }
    </div>
    <div class="key-updates">
        <h4>Key Updates:</h4>
        <ul>
            <li>{INSIGHT_POINT_1}</li>
            <li>{INSIGHT_POINT_2}</li>
            <li>{INSIGHT_POINT_3}</li>
        </ul>
    </div>
    <div class="tags">
        <strong>Tags:</strong> <span>{TAG1}</span> <span>{TAG2}</span>
    </div>
    <div class="area-path">
        <strong>Area Path:</strong> <code>{FULL_AREA_PATH}</code>
    </div>
</div>
```

---

### 6. HTML Styling Specifications

#### CSS Template (Same styling as CloudInit reports)
```css
/* Base Styles */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    line-height: 1.6;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    padding: 20px;
    color: #333;
}

.container {
    max-width: 1400px;
    margin: 0 auto;
    background: white;
    border-radius: 12px;
    box-shadow: 0 10px 40px rgba(0,0,0,0.2);
    overflow: hidden;
}

/* Header Styles */
.header {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    padding: 40px;
    text-align: center;
}

.header h1 {
    font-size: 2.5em;
    margin-bottom: 15px;
}

.metadata {
    font-size: 0.95em;
    opacity: 0.9;
    margin-top: 10px;
}

/* Executive Summary */
.executive-summary {
    background: linear-gradient(135deg, #e3f2fd 0%, #bbdefb 100%);
    padding: 25px;
    border-radius: 8px;
    margin-bottom: 30px;
    border-left: 5px solid #2196f3;
}

/* Stats Grid */
.stats-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 15px;
    margin-top: 20px;
}

.stat-card {
    background: white;
    padding: 20px;
    border-radius: 8px;
    text-align: center;
    box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.stat-number {
    font-size: 2.5em;
    font-weight: bold;
    color: #667eea;
}

.stat-label {
    font-size: 0.9em;
    color: #666;
    margin-top: 5px;
}

/* Area Section Headers */
.area-section {
    margin: 40px 0;
    padding-top: 20px;
    border-top: 3px solid #667eea;
}

.area-section h2 {
    color: #667eea;
    font-size: 2em;
    margin-bottom: 20px;
}

.area-section h2::before {
    content: "📁 ";
}

/* Epic Cards */
.epic-card {
    background: #f8f9fa;
    border-left: 4px solid #667eea;
    padding: 20px;
    margin-bottom: 25px;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0,0,0,0.05);
}

.epic-card h3 {
    color: #2c3e50;
    margin-bottom: 10px;
    font-size: 1.3em;
}

.epic-meta {
    color: #666;
    font-size: 0.9em;
    margin-bottom: 12px;
    padding: 8px 0;
    border-bottom: 1px solid #dee2e6;
}

/* Child Features Section */
.child-features {
    margin-top: 20px;
    padding: 15px;
    background: #fff;
    border-radius: 6px;
    border: 1px solid #e0e0e0;
}

.child-features h4 {
    color: #495057;
    font-size: 1.05em;
    margin-bottom: 12px;
    padding-bottom: 8px;
    border-bottom: 2px solid #dee2e6;
}

.feature-list {
    list-style: none;
    padding: 0;
    margin: 0;
}

.feature-list li {
    padding: 10px;
    margin: 8px 0;
    background: #f8f9fa;
    border-left: 3px solid #4caf50;
    border-radius: 4px;
    transition: all 0.2s ease;
}

.feature-list li:hover {
    background: #e8f5e9;
    border-left-width: 5px;
    transform: translateX(2px);
}

.feature-list a {
    color: #1976d2;
    text-decoration: none;
    font-weight: 500;
    font-size: 0.95em;
}

.feature-list a:hover {
    color: #0d47a1;
    text-decoration: underline;
}

.feature-meta {
    display: block;
    margin-top: 4px;
    font-size: 0.85em;
    color: #666;
    font-style: italic;
}

.no-features {
    color: #999;
    font-style: italic;
    padding: 10px;
    text-align: center;
}

/* Badges */
.badge {
    display: inline-block;
    padding: 4px 12px;
    border-radius: 12px;
    font-size: 0.85em;
    font-weight: 600;
    margin: 2px 4px 2px 0;
}

.badge-committed {
    background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
    color: white;
}

.badge-multi-semester {
    background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
    color: white;
}

.badge-current {
    background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
    color: white;
}

.badge-review {
    background: linear-gradient(135deg, #f7971e 0%, #ffd200 100%);
    color: #333;
}

.badge-deferred {
    background: linear-gradient(135deg, #bdc3c7 0%, #2c3e50 100%);
    color: white;
}

.badge-done {
    background: linear-gradient(135deg, #56ab2f 0%, #a8e063 100%);
    color: white;
}

/* Data Tables */
.data-table {
    width: 100%;
    border-collapse: collapse;
    margin: 20px 0;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    border-radius: 8px;
    overflow: hidden;
}

.data-table th {
    background: linear-gradient(135deg, #434343 0%, #000000 100%);
    color: white;
    padding: 14px;
    text-align: left;
    font-size: 0.95em;
    font-weight: 600;
}

.data-table td {
    padding: 12px 14px;
    border-bottom: 1px solid #e0e0e0;
}

.data-table tr:hover {
    background: #f8f9fa;
}

.data-table a {
    color: #667eea;
    text-decoration: none;
    font-weight: 500;
}

.data-table a:hover {
    color: #764ba2;
    text-decoration: underline;
}

/* Insights Box */
.insights-box {
    background: linear-gradient(135deg, #fff9c4 0%, #fff59d 100%);
    padding: 25px;
    border-radius: 8px;
    margin: 30px 0;
    border-left: 5px solid #fbc02d;
}

.insights-box h2 {
    color: #f57f17;
    border: none;
    font-size: 1.8em;
    margin-bottom: 20px;
}

.insights-box h3 {
    color: #f9a825;
    font-size: 1.3em;
    margin: 20px 0 12px 0;
}

/* Cross-Area Comparison */
.comparison-section {
    background: linear-gradient(135deg, #e8eaf6 0%, #c5cae9 100%);
    padding: 25px;
    border-radius: 8px;
    margin: 30px 0;
    border-left: 5px solid #5c6bc0;
}

.comparison-section h2 {
    color: #3949ab;
    border: none;
}

/* Footer */
.footer {
    background: #f5f5f5;
    padding: 30px;
    text-align: left;
    color: #666;
    font-size: 0.9em;
    border-top: 3px solid #e0e0e0;
}

/* Responsive Design */
@media (max-width: 768px) {
    .stats-grid {
        grid-template-columns: 1fr;
    }
    .content {
        padding: 20px;
    }
}

@media print {
    body {
        background: white;
        padding: 0;
    }
    .container {
        box-shadow: none;
    }
}
```

---

### 7. Azure DevOps URL Construction

#### Work Item Links
```
Base URL: https://msazure.visualstudio.com/b32aa71e-8ed2-41b2-9d77-5bc261222004/_workitems/edit/{WorkItemId}

Markdown Format:
[{WorkItemId}](https://msazure.visualstudio.com/b32aa71e-8ed2-41b2-9d77-5bc261222004/_workitems/edit/{WorkItemId})

HTML Format:
<a href="https://msazure.visualstudio.com/b32aa71e-8ed2-41b2-9d77-5bc261222004/_workitems/edit/{WorkItemId}" target="_blank">{WorkItemId}</a>
```

#### Azure DevOps Query Links

**Purpose**: Provide users with direct links to Azure DevOps queries showing Epics and their child Features in proper hierarchy, **filtered by Area Path and Iteration Path**.

**⚠️ CRITICAL: Query links MUST filter by both Area Path AND Iteration Path to match report scope!**

**Query Link Construction:**
```
Base Query URL Format:
https://msazure.visualstudio.com/One/_workitems?wiql={ENCODED_WIQL}

⚠️ ALWAYS construct custom WIQL query with area + iteration filters
⚠️ DO NOT use generic query links without filters
⚠️ Query results MUST match the work items shown in the report

WIQL Template for Epic→Feature Hierarchy (WITH Iteration Filter):
SELECT
    [System.Id],
    [System.Title],
    [System.WorkItemType],
    [System.State],
    [System.AssignedTo],
    [Custom.CommitmentStatus]
FROM workitemLinks
WHERE
    (
        Source.[System.TeamProject] = 'One'
        AND Source.[System.WorkItemType] = 'Epic'
        AND Source.[System.AreaPath] UNDER '{AREA_PATH}'
        AND Source.[System.IterationPath] = '{ITERATION_PATH}'
    )
    AND (
        [System.Links.LinkType] = 'System.LinkTypes.Hierarchy-Forward'
    )
    AND (
        Target.[System.WorkItemType] = 'Feature'
    )
MODE (Recursive)

WIQL Template for Epic→Feature Hierarchy (WITHOUT Iteration Filter):
Use this ONLY when includeAllIterations=true OR iterationPath=null
SELECT
    [System.Id],
    [System.Title],
    [System.WorkItemType],
    [System.State],
    [System.AssignedTo],
    [Custom.CommitmentStatus]
FROM workitemLinks
WHERE
    (
        Source.[System.TeamProject] = 'One'
        AND Source.[System.WorkItemType] = 'Epic'
        AND Source.[System.AreaPath] UNDER '{AREA_PATH}'
    )
    AND (
        [System.Links.LinkType] = 'System.LinkTypes.Hierarchy-Forward'
    )
    AND (
        Target.[System.WorkItemType] = 'Feature'
    )
MODE (Recursive)

WIQL Template for All Work Items (Epics + Features) WITH Iteration Filter:
SELECT
    [System.Id],
    [System.Title],
    [System.WorkItemType],
    [System.State],
    [System.AssignedTo],
    [Custom.CommitmentStatus],
    [System.Tags],
    [System.IterationPath],
    [System.AreaPath]
FROM workitems
WHERE
    [System.TeamProject] = 'One'
    AND [System.WorkItemType] IN ('Epic', 'Feature')
    AND [System.AreaPath] UNDER '{AREA_PATH}'
    AND [System.IterationPath] UNDER '{ITERATION_PATH}'
    AND [System.State] <> 'Removed'
ORDER BY [System.ChangedDate] DESC

⚠️ **NOTE:** Changed from `=` to `UNDER` for IterationPath to include sub-iterations (e.g., 'One\Krypton' includes 'One\Krypton\Sprint1')

WIQL Template for All Work Items (Epics + Features) WITHOUT Iteration Filter:
Use this ONLY when includeAllIterations=true OR iterationPath=null
SELECT
    [System.Id],
    [System.Title],
    [System.WorkItemType],
    [System.State],
    [System.AssignedTo],
    [Custom.CommitmentStatus],
    [System.Tags],
    [System.IterationPath],
    [System.AreaPath]
FROM workitems
WHERE
    [System.TeamProject] = 'One'
    AND [System.WorkItemType] IN ('Epic', 'Feature')
    AND [System.AreaPath] UNDER '{AREA_PATH}'
    AND [System.State] <> 'Removed'
ORDER BY [System.ChangedDate] DESC

ITERATION FILTERING LOGIC:
1. DEFAULT: Include iteration filter with "One\Krypton" (or user-specified iterationPath)
2. EXCEPTION: Omit iteration filter ONLY if includeAllIterations=true OR iterationPath=null
3. ALWAYS document in report whether iteration filter is applied

Example Area Path: "One\\Azure Compute\\CPlat\\PIR\\Azure Compute Gallery"
Example Iteration Path: "One\\Krypton"
```

**URL Encoding:**
```
Steps to create query link:
1. Determine if iteration filter should be applied:
   - DEFAULT: YES - Use iteration filter with "One\\Krypton" (or user-specified value)
   - EXCEPTION: NO - Only if includeAllIterations=true OR iterationPath=null
   
2. Select appropriate WIQL template:
   - WITH iteration filter: Include "AND [System.IterationPath] UNDER '{ITERATION_PATH}'"
   - WITHOUT iteration filter: Omit iteration path condition
   
3. Build the WIQL query:
   - For HIERARCHICAL view (Epic → Feature tree): Use workitemLinks query
   - For FLAT view (all items): Use workitems query
   - Replace {AREA_PATH} with actual area path using SINGLE backslash (e.g., 'One\CloudInit')
   - Replace {ITERATION_PATH} with actual iteration using SINGLE backslash (e.g., 'One\Krypton')
   
4. URL encode using PowerShell method (RECOMMENDED):
   ```powershell
   $wiql = "SELECT [System.Id], [System.Title]... FROM workitemLinks WHERE..."
   $encoded = [System.Web.HttpUtility]::UrlEncode($wiql)
   ```
   
5. Construct final URL with _a=query parameter:
   https://msazure.visualstudio.com/One/_workitems?_a=query&wiql={ENCODED_WIQL}

⚠️ CRITICAL ENCODING RULES:
- Use PowerShell [System.Web.HttpUtility]::UrlEncode() for accurate encoding
- WIQL must use SINGLE backslash in area paths: 'One\CloudInit' (not 'One\\CloudInit')
- After encoding: Single backslash becomes %5c (lowercase!)
- Must include _a=query parameter in URL for proper Azure DevOps handling
- Spaces encode as + (plus sign)
- Square brackets encode as %5b and %5d (lowercase)

COMPLETE EXAMPLE 1 - HIERARCHICAL VIEW (Epic → Feature Tree):

Area Path: One\CloudInit
Iteration Path: One\Krypton

PowerShell Encoding:
```powershell
$wiql = "SELECT [System.Id], [System.Title], [System.WorkItemType], [System.State], [System.AssignedTo], [System.Tags], [System.IterationPath], [System.AreaPath], [Custom.CommitmentStatus] FROM workitemLinks WHERE ([Source].[System.TeamProject] = 'One' AND [Source].[System.WorkItemType] = 'Epic' AND [Source].[System.AreaPath] UNDER 'One\CloudInit' AND [Source].[System.IterationPath] UNDER 'One\Krypton' AND [Source].[System.State] <> 'Removed') AND ([System.Links.LinkType] = 'System.LinkTypes.Hierarchy-Forward') AND ([Target].[System.WorkItemType] = 'Feature') ORDER BY [System.Id] MODE (Recursive)"
$encoded = [System.Web.HttpUtility]::UrlEncode($wiql)
```

Final URL:
https://msazure.visualstudio.com/One/_workitems?_a=query&wiql=SELECT+%5bSystem.Id%5d%2c+%5bSystem.Title%5d%2c+%5bSystem.WorkItemType%5d%2c+%5bSystem.State%5d%2c+%5bSystem.AssignedTo%5d%2c+%5bSystem.Tags%5d%2c+%5bSystem.IterationPath%5d%2c+%5bSystem.AreaPath%5d%2c+%5bCustom.CommitmentStatus%5d+FROM+workitemLinks+WHERE+(%5bSource%5d.%5bSystem.TeamProject%5d+%3d+%27One%27+AND+%5bSource%5d.%5bSystem.WorkItemType%5d+%3d+%27Epic%27+AND+%5bSource%5d.%5bSystem.AreaPath%5d+UNDER+%27One%5cCloudInit%27+AND+%5bSource%5d.%5bSystem.IterationPath%5d+UNDER+%27One%5cKrypton%27+AND+%5bSource%5d.%5bSystem.State%5d+%3c%3e+%27Removed%27)+AND+(%5bSystem.Links.LinkType%5d+%3d+%27System.LinkTypes.Hierarchy-Forward%27)+AND+(%5bTarget%5d.%5bSystem.WorkItemType%5d+%3d+%27Feature%27)+ORDER+BY+%5bSystem.Id%5d+MODE+(Recursive)

Result: Shows collapsible Epic nodes with Features indented underneath (tree view)

COMPLETE EXAMPLE 2 - FLAT VIEW (All Items):

Area Path: One\CloudInit
Iteration Path: One\Krypton

PowerShell Encoding:
```powershell
$wiql = "SELECT [System.Id], [System.Title], [System.WorkItemType], [System.State], [System.AssignedTo], [System.Tags], [System.IterationPath], [System.AreaPath], [Custom.CommitmentStatus] FROM workitems WHERE [System.TeamProject] = 'One' AND [System.AreaPath] UNDER 'One\CloudInit' AND [System.IterationPath] UNDER 'One\Krypton' AND [System.WorkItemType] IN ('Epic', 'Feature') AND [System.State] <> 'Removed' ORDER BY [System.ChangedDate] DESC"
$encoded = [System.Web.HttpUtility]::UrlEncode($wiql)
```

Result: Shows all Epics and Features in flat list, ordered by most recently changed

⚠️ VALIDATION CHECKLIST:
✓ URL includes _a=query parameter
✓ Encoded with [System.Web.HttpUtility]::UrlEncode()
✓ Single backslash in WIQL (e.g., 'One\CloudInit')
✓ After encoding: backslash becomes %5c (lowercase)
✓ Clicking link opens Azure DevOps with correct filters applied
✓ Work items filtered by Area Path and Iteration Path
✓ Only Epic and Feature types shown
✓ Removed state excluded
✓ Results match work items displayed in report
✓ For hierarchical: Epic nodes are collapsible with Features nested
```

**HTML Format:**
```html
<div class="query-link">
    <strong>View Work Items in Azure DevOps:</strong> 
    <a href="{QUERY_URL}" target="_blank" class="ado-query-btn">ADO Query</a>
    <div class="query-details">
        Filtered by: Area Path = {AREA_PATH}{If iteration filter: , Iteration = {ITERATION_PATH}}
    </div>
</div>
```

**Note:** 
- The link text should be exactly "ADO Query" as specified by user requirements
- Query link MUST include both area path filter and iteration path filter (unless includeAllIterations=true)
- Display what filters are applied in the query details section for transparency

**CSS for Query Link Button:**
```css
.query-link {
    background: linear-gradient(135deg, #e8f5e9 0%, #c8e6c9 100%);
    padding: 15px 20px;
    border-radius: 8px;
    margin: 20px 0;
    border-left: 4px solid #4caf50;
}

.query-details {
    font-size: 0.85em;
    color: #555;
    margin-top: 8px;
    font-style: italic;
}

.ado-query-btn {
    display: inline-block;
    background: linear-gradient(135deg, #4caf50 0%, #45a049 100%);
    color: white !important;
    padding: 8px 16px;
    border-radius: 6px;
    text-decoration: none;
    font-weight: 600;
    margin-left: 10px;
    transition: all 0.3s ease;
}

.ado-query-btn:hover {
    background: linear-gradient(135deg, #45a049 0%, #3d8b40 100%);
    box-shadow: 0 4px 8px rgba(0,0,0,0.2);
    transform: translateY(-2px);
}
```

**Placement in Reports:**
- **Unified Mode**: Place query link in report header (below metadata) - query shows all areas combined OR link to each area's specific query
- **Separate Mode**: Place query link in report header (below metadata) for specific area
- **Both Modes**: Query link should appear prominently in header with "ADO Query" as link text
- **Critical**: Query MUST filter by Area Path AND Iteration Path (default: "One\Krypton") to match report scope

**Example Usage in Report Header:**
```html
<div class="header">
    <h1>📦 Extension Publishing Status Report</h1>
    <div class="subtitle">Azure DevOps Work Items Report</div>
    <div class="metadata">
        <strong>Area Path:</strong> One\Azure Compute\CPlat\PIR\Extension Publishing<br>
        <strong>Iteration:</strong> One\Krypton<br>
        <strong>Generated:</strong> October 16, 2025<br>
        <strong>Project:</strong> One
    </div>
    <div class="query-link">
        <strong>View Work Items in Azure DevOps:</strong> 
        <a href="{QUERY_URL_WITH_AREA_AND_ITERATION_FILTERS}" target="_blank" class="ado-query-btn">ADO Query</a>
        <div class="query-details">
            Filtered by: Area Path = One\Azure Compute\CPlat\PIR\Extension Publishing, Iteration = One\Krypton
        </div>
    </div>
</div>
```

**⚠️ IMPORTANT - Query Link Requirements:**
1. **MUST include Area Path filter**: Query should filter `[System.AreaPath] UNDER '{AREA_PATH}'`
2. **MUST include Iteration Path filter** (by default): Query should filter `[System.IterationPath] = '{ITERATION_PATH}'`
3. **Iteration filter omitted ONLY when**: includeAllIterations=true OR iterationPath=null
4. **Query results MUST match report data**: Same work items shown in ADO as in the generated report
5. **Display filter details**: Show users what filters are applied in the query

**Query Link Validation:**
- Test that query link opens correctly in Azure DevOps
- Verify hierarchy is displayed (parent Epics with child Features)
- Ensure Area Path filter is applied correctly (shows only items UNDER specified area)
- Verify Iteration Path filter is applied correctly (shows only items in specified iteration)
- Confirm query results match report data exactly
- Validate that excluded states (e.g., "Removed") are not shown
- Check that only Epic and Feature work item types are displayed

**Query Link Checklist:**
```
Before generating report, verify query link:
✓ Area Path filter included: [System.AreaPath] UNDER '{AREA_PATH}'
✓ Iteration Path filter included (unless includeAllIterations=true): [System.IterationPath] = '{ITERATION_PATH}'
✓ Work item types filtered: [System.WorkItemType] IN ('Epic', 'Feature')
✓ Removed state excluded: [System.State] <> 'Removed'
✓ URL properly encoded
✓ Query details displayed showing active filters
✓ Link opens in new tab (target="_blank")
✓ Query results match report contents
```

---

### 8. File Naming Conventions

#### Unified Report Mode
```
HTML:
Reports/Multi_Area_CRM_Report_{YYYYMMDD_HH}.html

Examples:
Reports/Multi_Area_CRM_Report_20251015_10.html
```

#### Separate Report Mode
```
Per area path:
Reports/{AREA_NAME}_Status_Report_{YYYYMMDD_HH}.html

Examples:
Reports/CRM_Status_Report_20251015_10.html
Reports/Sales_Status_Report_20251015_10.html
Reports/Marketing_Status_Report_20251015_10.html
```

#### Output Folder Structure
```
Workspace Root/
  ├── Reports/           ← All generated reports go here
  │   ├── Multi_Area_CRM_Report_20251015_10.html
  │   ├── CRM_Status_Report_20251015_10.html
  │   └── Sales_Status_Report_20251015_10.html
  └── Instructions/
      └── CRM_ADO_Report_Agent_Instructions.md
```

#### Area Name Extraction
```
From "One\CRM\CustomerEngagement" → "CRM"
From "One\Sales\Enterprise" → "Sales"
From "One\Marketing\Digital" → "Marketing"

Rules:
1. Split by backslash
2. Take second element (after "One")
3. Remove special characters
4. Use PascalCase if needed
```

---

### 9. Cross-Area Comparison Features

When `includeComparisons = true`, add comparative analysis.

**Note:** Comparisons work identically for Epics, Features, or mixed work item types. Use "Work Items" in labels when areas have mixed types, or use specific type names when all areas use the same type.

#### A. Resource Distribution Analysis
```markdown
### Resource Distribution

| Assignee | Total Work Items | Area Distribution | Load Assessment |
|----------|-----------------|-------------------|-----------------|
| {NAME}   | {COUNT}         | CRM: {X}, Sales: {Y} | 🔴 High |
| {NAME}   | {COUNT}         | Marketing: {X}    | 🟡 Medium |
...

**Insights:**
- {ASSIGNEE} is heavily loaded across {COUNT} areas
- {AREA} has {COUNT} unassigned {epics|features}
- Recommend redistributing work from {AREA1} to {AREA2}
```

#### B. Commitment Status Comparison
```markdown
### Commitment Status by Area

```
| Area | Type | Committed % | Under Review % | Deferred % |
|------|------|-------------|----------------|------------|
| CRM  | Epic | 45%         | 30%            | 15%        |
| Sales| Feature | 60%      | 20%            | 10%        |
...
```

**Insights:**
- {AREA} has highest commitment rate ({PERCENT}%)
- {AREA} has {COUNT} items stuck in review - needs attention
- Overall planning health: {ASSESSMENT}
```

#### C. Common Technology/Theme Analysis
```markdown
### Common Focus Areas Across Teams

**Security & Compliance:** {COUNT} work items
- CRM: {COUNT} - {WORK_ITEM_TITLES}
- Sales: {COUNT} - {WORK_ITEM_TITLES}

**Quality Improvements:** {COUNT} work items
- CRM: {COUNT} - {WORK_ITEM_TITLES}
- Marketing: {COUNT} - {WORK_ITEM_TITLES}

**Recommendations:**
- Create cross-team working group for {THEME}
- Share best practices from {AREA} with {AREA}
```

---

### 10. Work Item Insights Generation (Per Work Item)

**Note:** Insights generation logic applies identically to both Epics and Features. The same keyword analysis and categorization rules work for either work item type.

#### Insight Logic by Work Item Characteristics
```
Based on work item title and tags, generate 2-3 salient points:

Security-focused (keywords: security, secrets, vulnerability, compliance):
- "Security initiative requiring {ASSESSMENT}"
- "Compliance requirement with {TIMELINE}"
- "Risk mitigation for {SCENARIO}"

Quality-focused (keywords: quality, testing, automation, observability):
- "Quality improvement initiative for {COMPONENT}"
- "Automated testing expansion"
- "Observability enhancement for {SYSTEM}"

Modernization (keywords: modernize, migrate, upgrade, refactor):
- "Strategic modernization spanning {TIMEFRAME}"
- "Migration from {OLD} to {NEW}"
- "Architectural transformation initiative"

Customer-facing (keywords: customer, user, experience, UX):
- "Customer experience enhancement"
- "User-facing feature with {IMPACT}"
- "Addressing customer feedback on {TOPIC}"

Partner/External (tags: PartnerAsk, Producer, consumer):
- "External dependency requiring coordination"
- "Partner request from {PARTNER}"
- "Producer commitment with external visibility"

Multi-semester commitments:
- "Long-term initiative spanning multiple semesters"
- "Sustained engineering focus required"
- "Strategic investment with phased delivery"

Current semester commitments:
- "Current semester commitment - completion by {DATE}"
- "Time-sensitive delivery requirement"
- "Immediate priority item"

Deferred items:
- "Deferred pending {REASON}"
- "Resource constraints or dependency blocking"
- "Consider revisiting in {TIMEFRAME}"
```

---

## 🔍 Troubleshooting: Missing Features in Report

### Problem: Report shows fewer Features than expected

**Root Causes & Solutions:**

#### 1. **Low `top` Parameter in Search Query**
```
Problem: mcp_azure-devops_search_workitem with top=50 only returns 50 Features
Solution: Increase top parameter to 150+ for Feature queries
         Check if search results show "count: X, results: Y" where X > Y
         If X > Y, increase top parameter and re-query
```

#### 2. **Only Querying Child Features from Epics**
```
Problem: Only retrieving Features through Epic→Feature relationships
Solution: ALWAYS query Features independently first using searchText + areaPath
         Example: Query "Azure Compute Gallery" + workItemType=["Feature"]
         Then determine Epic relationships afterward
```

#### 3. **Missing areaPath Filter Parameter**
```
Problem: Using only searchText may miss Features with different naming
Solution: Use BOTH areaPath and searchText in query:
         areaPath: ["One\\Azure Compute\\CPlat\\PIR\\Azure Compute Gallery"]
         searchText: "Azure Compute Gallery"
```

#### 4. **Incorrect Iteration Filtering**
```
Problem: Filtering out Features that should be included
Solution: Verify iteration path matches exactly:
         Default: "One\\Krypton" (with double backslashes)
         Check Feature's System.IterationPath field matches filter
         Set includeAllIterations=true to bypass iteration filtering for testing
```

#### 5. **Features Excluded by State Filter**
```
Problem: Features have State="Removed" or other excluded states
Solution: Check excludeStates array (default: ["Removed"])
         Adjust exclusion list if needed
         Log excluded Features for visibility
```

### Validation Checklist Before Generating Report

```
Step 1: Count Total Features from Search
✓ Query: mcp_azure-devops_search_workitem with workItemType=["Feature"]
✓ Note: "count" field in response = Total Features available
✓ Note: Length of "results" array = Features retrieved
✓ If count > results length, increase top parameter!

Step 2: Apply Filters
✓ Filter by System.AreaPath starts with area path
✓ Filter by System.IterationPath (if not includeAllIterations)
✓ Filter by System.State (exclude "Removed")
✓ Count = Features after filtering

Step 3: Identify Relationships
✓ Query Epics with expand="relations"
✓ Extract all child Feature IDs from Epic relations
✓ ChildFeatures = Set of Feature IDs linked to Epics
✓ OrphanFeatures = AllFeatures - ChildFeatures

Step 4: Validate Totals
✓ AllFeatures.length = ChildFeatures.length + OrphanFeatures.length
✓ If equation doesn't balance, Features were missed!
✓ Re-query with higher top parameter or broader search

Step 5: Report Structure
✓ Epics Section: Show Epics with their child Features
✓ Orphan Features Section: Show Features NOT in ChildFeatures set
✓ Summary: Total should match AllFeatures count
```

### Example: Azure Compute Gallery Scenario

```
Expected Results:
- Search finds: 95 total work items (Epics + Features)
- After filtering by area + iteration: 14 Features
- Epic relationships: 1 Feature has parent Epic
- Calculation: 14 Features = 1 (with parent) + 13 (orphans)

Report Must Show:
✓ 1 Epic with 1 child Feature (Feature 33420369 under Epic 32794172)
✓ 13 Orphan Features listed separately:
  - Feature 33750398 (ARM Echo Falls 2SNC)
  - Feature 15240942 (Quota to ARG)
  - Feature 4745249 (Direct Shared Gallery)
  - Feature 34746238 (Reddog Migration)
  - Feature 35390783 (ACN Onboarding)
  - ... (8 more Features)
✓ Total: 14 Features displayed in report

If Report Shows < 14 Features:
❌ Features were missed during search or filtering
❌ Increase top parameter in search query
❌ Verify areaPath filter is working correctly
❌ Check iteration filter isn't excluding Features incorrectly
```

### Debug Logging Recommendations

Add logging to track Feature retrieval:
```
Log: "Querying Features for area: {AREA_PATH}"
Log: "Search returned: {COUNT} total, {RETRIEVED} retrieved"
Log: "After area filter: {COUNT} Features"
Log: "After iteration filter: {COUNT} Features"
Log: "After state exclusion: {COUNT} Features"
Log: "Features with parent Epics: {COUNT}"
Log: "Orphan Features: {COUNT}"
Log: "VALIDATION: {PARENT_COUNT} + {ORPHAN_COUNT} = {TOTAL_COUNT}"
```

---

## 11. Data Validation & Error Handling

#### Pre-Report Generation Validation
```
For each area path:
1. Verify area path exists in Azure DevOps
2. Check if any epics found
3. Validate commitment status field availability
4. Verify assignee data completeness

Error Handling:
- No epics found: Include section noting "No active epics for {AREA}"
- Missing commitment status: Note in report and exclude from commitment charts
- Unassigned epics: Display as "Unassigned" in tables
- Missing tags: Show as "No tags"
```

#### Data Completeness Report
```
At end of each section or report:

Data Completeness:
- Epics with commitment status: {COUNT}/{TOTAL} ({PERCENT}%)
- Epics with assignees: {COUNT}/{TOTAL} ({PERCENT}%)
- Epics with tags: {COUNT}/{TOTAL} ({PERCENT}%)
- Average fields populated: {PERCENT}%
```

---

### 12. Usage Examples

#### Example 1: Unified Report for Multiple CRM Areas (Default Iteration Filter)
```
Input:
{
    "project": "One",
    "areaPaths": [
        "One\\CRM\\CustomerEngagement",
        "One\\CRM\\Sales",
        "One\\CRM\\Marketing"
    ],
    "reportMode": "unified",
    "outputFormat": ["html"],
    "includeComparisons": true
    // iterationPath defaults to "One\\Krypton"
}

Output:
- Reports/Multi_Area_CRM_Report_20251015_10.html

Content: Single HTML report with sections for each area + cross-area insights
Filtering: Only work items in iteration "One\Krypton"
ADO Query Link: Included in header with "ADO Query" link text
```

#### Example 2: Separate Reports Per Area (All Iterations)
```
Input:
{
    "project": "One",
    "areaPaths": [
        "One\\CRM\\CustomerEngagement",
        "One\\CRM\\Sales",
        "One\\CRM\\Marketing"
    ],
    "reportMode": "separate",
    "outputFormat": ["html"],
    "includeComparisons": false,
    "includeAllIterations": true
}

Output:
- Reports/CustomerEngagement_Status_Report_20251015_10.html
- Reports/Sales_Status_Report_20251015_10.html
- Reports/Marketing_Status_Report_20251015_10.html

Content: 3 HTML files total, each area gets dedicated report
Filtering: Includes work items from all iterations
ADO Query Link: Each report includes area-specific query link with "ADO Query" text
```

#### Example 3: Custom Iteration Filter
```
Input:
{
    "project": "One",
    "areaPaths": ["One\\CRM"],
    "reportMode": "unified",
    "outputFormat": ["html"],
    "iterationPath": "One\\Argon"
}

Output:
- Reports/Multi_Area_CRM_Report_20251015_10.html

Content: HTML report filtered to iteration "One\Argon"
ADO Query Link: Query filtered by area path and Argon iteration
```

#### Example 4: User Request Interpretation
```
User says: "Generate CRM report for all areas"
→ Use unified mode with all CRM sub-areas
→ Default iteration filter: "One\Krypton"

User says: "Generate separate reports for CRM and Sales"
→ Use separate mode with 2 area paths
→ Default iteration filter: "One\Krypton"

User says: "Show me CRM, Sales, and Marketing together"
→ Use unified mode with 3 area paths and comparisons
→ Default iteration filter: "One\Krypton"

User says: "Create individual reports for each CRM team"
→ Use separate mode with all CRM sub-area paths
→ Default iteration filter: "One\Krypton"

User says: "Pull all ADO items for CRM"
→ Set includeAllIterations=true
→ Include work items from all iterations

User says: "Generate report for Argon iteration"
→ Set iterationPath="One\\Argon"
→ Override default iteration filter

User says: "Show CRM items with no iteration filter"
→ Set includeAllIterations=true OR iterationPath=null
→ Include work items from all iterations
```

---

### 13. MCP Query Strategy

**REMINDER**: Use Azure DevOps MCP Server tools exclusively - NO scripts!

#### Area Path Discovery
```
If user provides high-level area (e.g., "CRM"):

Step 1: Query all epics under parent area using MCP Server
mcp_azure-devops_search_workitem:
  project: ["One"]
  searchText: "CRM"
  workItemType: ["Epic"]
  top: 100

Step 2: Extract unique area paths from results
unique_areas = extract_area_paths(results)
# Returns: ["One\\CRM\\Sales", "One\\CRM\\Marketing", ...]

Step 3: Ask user for confirmation or auto-include sub-areas

IMPORTANT: Do NOT create PowerShell or Python scripts to query area paths!
```

#### Optimized Batch Queries
```
Strategy: Minimize MCP API calls (NOT script-based queries!)

1. Single MCP search query across all areas (if under 100 total epics)
2. Batch MCP get work item details for all IDs at once
3. Filter and categorize in memory by area path

Instead of:
- MCP Query area 1 (1 call)
- MCP Get details area 1 (1 call)
- MCP Query area 2 (1 call)
- MCP Get details area 2 (1 call)
Total: 4 MCP calls

Do:
- MCP Query all areas (1 call)
- MCP Get all details in batch (1 call)
- Filter locally by area path
Total: 2 MCP calls

IMPORTANT: All queries must use MCP Server tools, NOT scripts!
```

---

### 14. Quality Assurance Checklist

**⚠️ MANDATORY VALIDATION - DO NOT SKIP!**

Before generating final reports, complete ALL validation steps:

**Stage 1: Data Collection Validation (CRITICAL)**
- [ ] ✅ Total Epics from search query = N (logged and verified)
- [ ] ✅ Retrieved metadata for ALL N Epics (no missing Epics)
- [ ] ✅ Retrieved relations for ALL N Epics (processed in batches if N > 10)
- [ ] ✅ Retrieved Feature details for ALL referenced Features
- [ ] ✅ Counts verified: N Epics collected → N Epic cards will be in report
- [ ] ❌ NO placeholder text planned (e.g., "Continue with remaining epics...")
- [ ] ❌ If ANY validation fails, DO NOT proceed - collect missing data first

**Stage 2: Data Quality Validation**
- [ ] All area paths validated and contain epics
- [ ] Commitment statuses correctly categorized
- [ ] Epic counts match across summary tables
- [ ] No "Removed" state epics included (unless explicitly requested)
- [ ] All hyperlinks functional and point to correct work items
- [ ] Feature state filtering applied correctly (Done/Completed/Removed excluded)
- [ ] Epic→Feature relationships accurate (no orphaned Features)

**Stage 3: Formatting Validation (USE EXISTING REPORT AS TEMPLATE)**
- [ ] ✅ Read previous/reference report HTML for exact formatting
- [ ] ✅ CSS styles copied exactly from template (colors, gradients, spacing)
- [ ] ✅ HTML structure preserved (same classes, same layout, same sections)
- [ ] ✅ JavaScript copied exactly (toggle functions, interactions)
- [ ] ✅ ONLY data content will differ, NOT formatting/styling
- [ ] ❌ Did NOT create new HTML structure or change design
- [ ] ❌ Did NOT change colors, gradients, or visual appearance

**Stage 4: Content Completeness Validation**
- [ ] Executive summary accurate and comprehensive
- [ ] Each area section complete with all subsections
- [ ] ALL N Epic cards present in HTML (no skipped Epics)
- [ ] Features displayed correctly (child Features under Epics, orphans separately)
- [ ] Azure DevOps query link included with correct filters
- [ ] Report metadata complete (counts, timestamps, iteration info)

**Stage 5: Pre-Generation Final Check**
- [ ] ✅ Retrieved N Epics → Report will show N Epic cards ✓
- [ ] ✅ Retrieved M Features → Report will reference M Features ✓
- [ ] ✅ Template HTML loaded for formatting consistency ✓
- [ ] ✅ No placeholder/incomplete sections in planned output ✓
- [ ] ✅ Quality confirmed - ready to generate complete report ✓

**STOP CONDITIONS - DO NOT GENERATE REPORT IF:**
- ❌ Epic count mismatch (collected X but only Y in report)
- ❌ Missing Epic relations data (incomplete batch processing)
- ❌ Missing Feature details (incomplete data collection)
- ❌ Placeholder text present ("Continue with...", "...and N more...")
- ❌ Formatting template not loaded (risk of design regression)

**IF ANY STOP CONDITION IS TRUE:**
1. Log the specific issue
2. Go back to appropriate stage (data collection or template loading)
3. Complete missing steps
4. Re-run validation checklist
5. Only generate report when ALL validations pass
- [ ] Key insights generated for all active epics
- [ ] Cross-area comparisons meaningful (if enabled)
- [ ] Report metadata complete and accurate

**File Naming:**
- [ ] Timestamps correct and consistent
- [ ] Area names properly extracted and formatted
- [ ] Both MD and HTML generated (if requested)
- [ ] File names follow conventions exactly

**Accessibility:**
- [ ] Print-friendly styling included
- [ ] Color contrast sufficient for readability
- [ ] Links clearly distinguishable
- [ ] Tables have proper headers

---

### 15. Performance Optimization

#### For Large Area Path Lists (>5 areas)
```
1. Parallelize data collection where possible
2. Use streaming output for very large reports
3. Consider pagination for 50+ epics per area
4. Generate HTML incrementally rather than in memory
5. Add progress indicators for long-running operations
```

#### Caching Strategy
```
If generating multiple formats or modes:
1. Cache epic data after initial retrieval
2. Reuse categorization logic
3. Only regenerate formatting layer
4. Don't re-query Azure DevOps unnecessarily
```

---

### 16. Extensibility & Customization

#### Custom Sections
```
Allow users to add custom analysis sections:

customSections: [
    {
        title: "Timeline Analysis",
        type: "chart",
        data: "epic_completion_trends"
    },
    {
        title: "Tag Cloud",
        type: "visualization",
        data: "tag_frequency"
    }
]
```

#### Custom Filters
```
additionalFilters: {
    tags: ["HighPriority", "CustomerCritical"],
    assignees: ["user1@microsoft.com", "user2@microsoft.com"],
    updatedSince: "2025-10-01"
}
```

---

### 17. Report Footer Template

```markdown
## 📊 Report Details

**Report Configuration:**
- Report Mode: {Unified | Separate}
- Total Areas Analyzed: {COUNT}
- Area Paths: {LIST_OF_AREAS}
- Iteration Filter: {ITERATION_PATH | "All Iterations"}
- Total Work Items: {TOTAL_COUNT} ({EPIC_COUNT} Epics, {FEATURE_COUNT} Features)
- Standalone Features: {COUNT} (not linked to Epics)
- Child Features Retrieved: {COUNT} (linked to Epics)
- Report Generation Time: {TIMESTAMP}
- Data Source: Live Azure DevOps via MCP
- Project: One
- Excluded States: {LIST}
- Output Format: HTML
- Output Location: Reports/ folder
- Cross-Area Comparisons: {Enabled | Disabled}
- ADO Query Link: Included with "ADO Query" link text

**Area Breakdown:**
{For each area:}
- {AREA_NAME}: {EPIC_COUNT} Epics, {FEATURE_COUNT} Features
  - Standalone Features: {COUNT}
  - Active/Committed: {COUNT}
  - Under Review: {COUNT}
  - Deferred: {COUNT}
  - Done: {COUNT}
  {If Epics with child Features:}
  - Child Features under Epics: {COUNT}

**Iteration Filtering:**
{If iteration filter applied:}
- Iteration Path: {ITERATION_PATH}
- All work items filtered to this iteration
- To include all iterations, set includeAllIterations=true
{If all iterations included:}
- No iteration filter applied
- Report includes work items from all iterations

**Work Item Distribution:**
- Total Epics: {COUNT}
- Total Features: {COUNT}
  - Standalone Features (not linked to Epics): {COUNT}
  - Child Features (linked to Epics): {COUNT}
- Note: Some Features may appear both as standalone and linked to Epics

**Data Freshness:**
- All data pulled in real-time from Azure DevOps
- Represents current state as of {TIMESTAMP}
- Commitment statuses and assignments reflect latest updates
- Epic→Feature hierarchy retrieved using expand="relations"
- Iteration filtering: {APPLIED | NOT APPLIED}

**Azure DevOps Query Link:**
- Use the "ADO Query" link at the top of this report to see the same work items in ADO
- Query displays work items filtered by area path (and iteration if specified)
- Filters match report scope (area path, iteration, work item types)
- Query includes iteration filter: {YES | NO}

---

*This report provides comprehensive multi-area visibility into work item portfolios with Epic→Feature hierarchy. 
Features are displayed both as standalone items and as children under Epics.
Default iteration filter: One\Krypton (can be overridden or disabled).
Reports are saved to the Reports/ folder.
For detailed task-level tracking, refer to individual work items in Azure DevOps.*
```

---

## Summary

This CRM_ADO_report generator provides:

✅ **Multi-Area Support**: Handle 1 to N area paths in single execution
✅ **Flexible Modes**: Unified report OR separate per-area reports
✅ **Consistent Styling**: Same beautiful HTML format as CloudInit reports
✅ **Cross-Area Insights**: Optional comparative analysis across areas
✅ **Scalable**: Optimized for performance with many areas
✅ **Customizable**: Extensible with custom sections and filters
✅ **Production-Ready**: Complete error handling and validation
✅ **Epic→Feature Hierarchy**: Automatically retrieves and displays child Features under Epics
✅ **Standalone Features**: Pulls and displays Features not linked to Epics
✅ **Iteration Filtering**: Default filter to "One\Krypton" with option to override or disable
✅ **Azure DevOps Query Links**: Direct "ADO Query" links filtered by area path
✅ **Organized Output**: All reports saved to Reports/ folder
✅ **HTML-Only**: Streamlined HTML report generation

**Key Advantages Over Single-Area Reports:**
- Executive visibility across multiple teams
- Resource allocation insights
- Standardization opportunities
- Reduced report generation time (batch processing)
- Consistent formatting and branding across organization
- **Full Epic→Feature hierarchy displayed in reports**
- **Standalone Features displayed separately from Epic children**
- **Iteration filtering for focused reporting (default: One\Krypton)**
- **One-click "ADO Query" access to filtered work items**
- **Organized Reports/ folder structure**

**Use this generator for:**
- CRM organization-wide reporting
- Leadership reviews across multiple teams
- Cross-team planning and coordination
- Resource allocation analysis
- Portfolio-level epic and feature tracking
- **Hierarchical work item visibility (Epic→Feature→Tasks)**
- **Standalone Feature tracking (Features not linked to Epics)**
- **Iteration-specific reporting (default: One\Krypton)**
- **Stakeholder reports with direct ADO access via "ADO Query" links**
- **Sprint/Iteration planning and review reports**

---

## 🔒 Non-Negotiable Requirements

### Azure DevOps MCP Server Usage
- **MUST** use Azure DevOps MCP Server for all data queries
- **MUST** use `mcp_azure-devops_search_workitem` for epic and feature searches
- **MUST** use `mcp_azure-devops_wit_get_work_items_batch_by_ids` for batch work item details
- **MUST** use `mcp_azure-devops_wit_get_work_item` with expand="relations" to get Epic→Feature hierarchy
- **NEVER** create PowerShell scripts to query Azure DevOps
- **NEVER** create Python scripts to query Azure DevOps
- **NEVER** use Azure DevOps REST API directly

### Report Generation Approach
- **MUST** generate reports by directly creating files with full content
- **MUST** embed all CSS styling within HTML files (no external files)
- **NEVER** create Python scripts (python-docx, jinja2, etc.) unless user approves
- **NEVER** create PowerShell scripts for formatting unless user approves
- **ONLY** create automation scripts if user explicitly requests or approves it

### Approval Required For Scripts
Create scripts ONLY if user:
- Explicitly says "create a script"
- Explicitly says "automate this"
- Explicitly approves after you ask permission
- Says "I want a reusable script"

**Default behavior: Direct file creation, NO scripts!**
