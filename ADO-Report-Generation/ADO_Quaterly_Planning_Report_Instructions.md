# Azure DevOps Semester Planning Report Instructions

## Overview
This document provides comprehensive instructions for generating semester planning reports for the CloudInit/AzLinux Provisioning team using the Azure DevOps MCP server. The planning report combines external dependencies awaiting decisions with internal Epic work items to provide a complete view for semester planning.

## Report Purpose>
Provide a focused, high-level view of work requiring planning decisions by combining:
1. External dependencies (New/Under Review status) awaiting commitment decisions
2. Internal Epic work items organized by commitment status for capacity planning

---

## Section 1: Data Sources

### Source 1: External Dependencies (All Commitment Statuses)

**Definition:**
External partner requests filed ON the CloudInit team across all commitment statuses for complete planning visibility.

**Filtering Criteria:**
- Area Path: `One\CloudInit`
- Tags: `PartnerAsk` AND `Producer`
- Commitment Status: `All` (Committed, New, Under Review, Deferred, Rejected)
- State: Exclude `Done` items
- Work Item Types: Feature, Epic

**Query Strategy:**
```python
mcp_azure-devops_search_workitem(
    project=["One"],
    searchText="PartnerAsk Producer AzLinux",
    top=100
)
# Then filter by:
# - Area Path contains "CloudInit"
# - Custom.CommitmentStatus in ["New", "Under Review"]
# - System.State != "Done"
```

### Source 2: Internal Epics

**Definition:**
Epic work items in the CloudInit area representing major initiatives and projects.

**Filtering Criteria:**
- Area Path: `One\CloudInit`
- Work Item Type: `Epic`
- State: Exclude `Done` items
- All commitment statuses included

**Query Strategy:**
```python
mcp_azure-devops_search_workitem(
    project=["One"],
    searchText="Epic CloudInit",
    workItemType=["Epic"],
    top=100
)
# Then filter by:
# - Area Path contains "CloudInit"
# - System.State != "Done"
```

---

## Section 2: Report Structure

### Required Sections:

#### 1. Executive Summary
```markdown
**Semester Planning Report**
**Generated:** {Date and Time} (Fresh Data Pull from Azure DevOps)
**Service:** AzLinux Provisioning (CloudInit)
**Planning Period:** {Semester Name}

### Summary
- **External Dependencies Awaiting Decision:** {count}
- **Epics Under Review:** {count}
- **Epics Committed (Current + Multi-Semester):** {count}
- **Other Epics:** {count}
```

#### 2. External Dependencies by Commitment Status

Organized by commitment status to show full dependency portfolio.

**Subsections:**
- Committed (Current + Multi-Semester)
- Under Review / New
- Deferred
- Rejected

```markdown
## 📋 External Dependencies by Commitment Status ({count})

Partner requests filed ON CloudInit team organized by commitment status.

### ✅ Committed ({count})
| ID | Title | Commitment | State | Assignee |

### 🔍 Under Review / New ({count})
| ID | Title | Commitment | State | Assignee |

### ⏸️ Deferred ({count})
| ID | Title | Commitment | State | Assignee |

### ⛔ Rejected ({count})
| ID | Title | Commitment | State | Assignee |
```

#### 3. Epics by Commitment Status

Three subsections organized for planning priority:

**A. Under Review ({count})**
```markdown
### 🔍 Under Review ({count})
Epics being evaluated for commitment - require planning decisions.

| ID | Title | State | Assignee |
|---|---|---|---|
| [{id}](url) | {title} | {state} | {assignee} |
```

**B. Committed - Current & Multi-Semester ({count})**
```markdown
### ✅ Committed ({count})
Epics with confirmed commitment (current semester or spanning multiple semesters).

| ID | Title | State | Commitment | Assignee |
|---|---|---|---|---|
| [{id}](url) | {title} | {state} | {commitment_status} | {assignee} |
```

**C. Other ({count})**
```markdown
### ⏸️ Other ({count})
Epics that are deferred, rejected, or in other states.

| ID | Title | State | Commitment | Assignee |
|---|---|---|---|---|
| [{id}](url) | {title} | {state} | {commitment_status} | {assignee} |
```

#### 4. Planning Insights

```markdown
## 💡 Planning Insights

### Capacity Considerations
- **Decision Required:** {count} external dependencies + {count} under review epics = {total} items need decisions
- **Active Commitments:** {count} committed epics requiring resources
- **Planning Risk:** High/Medium/Low based on decision backlog

### Key Focus Areas
- {Technology area 1}: {count} items
- {Technology area 2}: {count} items
- {Technology area 3}: {count} items

### Recommendations
1. **Prioritize Decisions:** {count} dependencies awaiting commitment
2. **Capacity Allocation:** {count} committed epics need staffing
3. **Review Backlog:** {count} under review items need evaluation
```

---

## Section 3: File Naming Convention

### HTML Format (Primary Output):
```
CloudInit_Planning_Report_{YYYYMMDD}_{HH}.html
```

**Note:** Generate HTML format only. Markdown format is deprecated.

### Examples:
- `CloudInit_Planning_Report_20251009_14.html`
- `CloudInit_Planning_Report_20251021_15.html`

---

## Section 4: Automation Workflow

### ⚠️ CRITICAL: Always Use Fresh Data

**ALWAYS perform fresh queries to Azure DevOps MCP server before generating any report.**

### Step-by-Step Process:

#### Step 1: Query External Dependencies (New/Under Review)

```python
# Execute fresh search query
dependency_results = mcp_azure-devops_search_workitem(
    project=["One"],
    searchText="PartnerAsk Producer AzLinux",
    top=100
)

# Extract IDs for CloudInit dependencies
dependency_ids = [
    item["fields"]["system.id"] 
    for item in dependency_results["results"]
    if "CloudInit" in item["fields"].get("system.areapath", "")
]

# Get full details
dependencies = mcp_azure-devops_wit_get_work_items_batch_by_ids(
    project="One",
    ids=dependency_ids,
    fields=[
        "System.Id",
        "System.Title",
        "System.State",
        "System.AssignedTo",
        "Custom.CommitmentStatus"
    ]
)

# Filter for all commitment statuses (exclude Done)
planning_dependencies = [
    item for item in dependencies
    if item["fields"].get("System.State") != "Done"
]
```

#### Step 2: Query Internal Epics

```python
# Execute fresh search query for Epics
epic_results = mcp_azure-devops_search_workitem(
    project=["One"],
    searchText="Epic",
    workItemType=["Epic"],
    top=100
)

# Extract IDs for CloudInit Epics
epic_ids = [
    item["fields"]["system.id"]
    for item in epic_results["results"]
    if "CloudInit" in item["fields"].get("system.areapath", "")
]

# Get full details
epics = mcp_azure-devops_wit_get_work_items_batch_by_ids(
    project="One",
    ids=epic_ids,
    fields=[
        "System.Id",
        "System.Title",
        "System.State",
        "System.AssignedTo",
        "Custom.CommitmentStatus"
    ]
)

# Filter out Done items
active_epics = [
    item for item in epics
    if item["fields"].get("System.State") != "Done"
]
```

#### Step 3: Categorize Epics by Commitment Status

```python
epics_by_commitment = {
    "Under Review": [],
    "Committed": [],
    "Other": []
}

for epic in active_epics:
    commitment = epic["fields"].get("Custom.CommitmentStatus", "")
    state = epic["fields"].get("System.State", "")
    
    # Under Review
    if commitment in ["New", "Under Review"]:
        epics_by_commitment["Under Review"].append(epic)
    
    # Committed (current semester or multi-semester)
    elif "Committed" in commitment:
        epics_by_commitment["Committed"].append(epic)
    
    # Other (Deferred, Rejected, Incomplete, etc.)
    else:
        epics_by_commitment["Other"].append(epic)
```

#### Step 4: Generate Report Sections

```python
# Calculate summary counts
total_dependencies = len(planning_dependencies)
total_under_review = len(epics_by_commitment["Under Review"])
total_committed = len(epics_by_commitment["Committed"])
total_other = len(epics_by_commitment["Other"])

# Generate Executive Summary
# Generate External Dependencies table
# Generate Epics by Commitment tables
# Generate Planning Insights
```

#### Step 5: Generate HTML Report

```python
# Generate HTML report only
timestamp = datetime.now().strftime("%Y%m%d_%H")
html_filename = f"CloudInit_Planning_Report_{timestamp}.html"

# Note: Markdown format is deprecated - generate HTML only
```

---

## Section 5: URL Construction

### Work Item URL Format:
```
https://msazure.visualstudio.com/b32aa71e-8ed2-41b2-9d77-5bc261222004/_workitems/edit/{WorkItemId}
```

### Markdown Hyperlink Format:
```markdown
[{WorkItemId}](https://msazure.visualstudio.com/b32aa71e-8ed2-41b2-9d77-5bc261222004/_workitems/edit/{WorkItemId})
```

### HTML Hyperlink Format:
```html
<a href="https://msazure.visualstudio.com/b32aa71e-8ed2-41b2-9d77-5bc261222004/_workitems/edit/{WorkItemId}" target="_blank">{WorkItemId}</a>
```

---

## Section 6: HTML Report Template

### Complete HTML Template:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CloudInit Semester Planning Report - {Date}</title>
    <style>
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
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 12px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            overflow: hidden;
        }
        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px;
            text-align: center;
        }
        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }
        .metadata {
            font-size: 0.9em;
            opacity: 0.9;
        }
        .content {
            padding: 30px;
        }
        .summary-box {
            background: linear-gradient(135deg, #e0f7fa 0%, #b2ebf2 100%);
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 20px;
            border-left: 5px solid #00acc1;
        }
        .summary-box h3 {
            color: #00695c;
            margin-bottom: 15px;
        }
        .summary-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-top: 15px;
        }
        .summary-item {
            background: white;
            padding: 15px;
            border-radius: 8px;
            text-align: center;
        }
        .summary-count {
            font-size: 2em;
            font-weight: bold;
            color: #667eea;
        }
        .summary-label {
            font-size: 0.9em;
            color: #666;
            margin-top: 5px;
        }
        .section {
            margin: 30px 0;
        }
        .section h2 {
            color: #667eea;
            border-bottom: 3px solid #764ba2;
            padding-bottom: 10px;
            margin-bottom: 20px;
            font-size: 1.8em;
        }
        .section h3 {
            color: #764ba2;
            margin: 20px 0 10px 0;
            font-size: 1.4em;
        }
        .work-item-table {
            width: 100%;
            border-collapse: collapse;
            margin: 15px 0;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            border-radius: 8px;
            overflow: hidden;
        }
        .work-item-table th {
            background: linear-gradient(135deg, #434343 0%, #000000 100%);
            color: white;
            padding: 12px;
            text-align: left;
            font-size: 0.9em;
        }
        .work-item-table td {
            padding: 12px;
            border-bottom: 1px solid #e0e0e0;
        }
        .work-item-table tr:hover {
            background: #f9f9f9;
            transform: translateX(3px);
            transition: all 0.2s ease;
        }
        .work-item-table a {
            color: #667eea;
            text-decoration: none;
            font-weight: 500;
        }
        .work-item-table a:hover {
            color: #764ba2;
            text-decoration: underline;
        }
        .status-review {
            display: inline-block;
            background: linear-gradient(135deg, #f7971e 0%, #ffd200 100%);
            color: #333;
            padding: 4px 12px;
            border-radius: 12px;
            font-size: 0.85em;
            font-weight: 600;
        }
        .status-committed {
            display: inline-block;
            background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
            color: white;
            padding: 4px 12px;
            border-radius: 12px;
            font-size: 0.85em;
            font-weight: 600;
        }
        .status-other {
            display: inline-block;
            background: linear-gradient(135deg, #bdc3c7 0%, #2c3e50 100%);
            color: white;
            padding: 4px 12px;
            border-radius: 12px;
            font-size: 0.85em;
            font-weight: 600;
        }
        .insights-box {
            background: linear-gradient(135deg, #fff9c4 0%, #fff59d 100%);
            padding: 20px;
            border-radius: 8px;
            margin: 20px 0;
            border-left: 5px solid #fbc02d;
        }
        .insights-box h2 {
            color: #f57f17;
            border: none;
            font-size: 1.5em;
            margin-bottom: 15px;
        }
        .insights-box h3 {
            color: #f9a825;
            font-size: 1.2em;
            margin: 15px 0 10px 0;
        }
        .insights-box ul, .insights-box ol {
            margin-left: 20px;
        }
        .insights-box li {
            margin: 8px 0;
        }
        .footer {
            background: #f5f5f5;
            padding: 20px;
            text-align: center;
            color: #666;
            font-size: 0.9em;
            border-top: 2px solid #e0e0e0;
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
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📅 CloudInit Semester Planning Report</h1>
            <div class="metadata">
                <p><strong>Generated:</strong> {Date and Time} (Fresh Data Pull from Azure DevOps)</p>
                <p><strong>Service:</strong> AzLinux Provisioning (CloudInit)</p>
                <p><strong>Planning Period:</strong> {Semester Name}</p>
            </div>
        </div>

        <div class="content">
            <div class="summary-box">
                <h3>📊 Planning Summary</h3>
                <div class="summary-grid">
                    <div class="summary-item">
                        <div class="summary-count">{count}</div>
                        <div class="summary-label">Dependencies Awaiting Decision</div>
                    </div>
                    <div class="summary-item">
                        <div class="summary-count">{count}</div>
                        <div class="summary-label">Epics Under Review</div>
                    </div>
                    <div class="summary-item">
                        <div class="summary-count">{count}</div>
                        <div class="summary-label">Committed Epics</div>
                    </div>
                    <div class="summary-item">
                        <div class="summary-count">{count}</div>
                        <div class="summary-label">Other Epics</div>
                    </div>
                </div>
            </div>

            <section class="section">
                <h2>📋 External Dependencies Awaiting Decision ({count})</h2>
                <p>Partner requests filed ON CloudInit team that require commitment decision for planning.</p>
                <table class="work-item-table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Title</th>
                            <th>Status</th>
                            <th>Assignee</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td><a href="{azure_devops_url}" target="_blank">{id}</a></td>
                            <td>{title}</td>
                            <td><span class="status-review">{status}</span></td>
                            <td>{assignee}</td>
                        </tr>
                        <!-- Additional rows -->
                    </tbody>
                </table>
            </section>

            <section class="section">
                <h2>🎯 Epics by Commitment Status</h2>
                
                <h3>🔍 Under Review ({count})</h3>
                <p>Epics being evaluated for commitment - require planning decisions.</p>
                <table class="work-item-table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Title</th>
                            <th>State</th>
                            <th>Assignee</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td><a href="{azure_devops_url}" target="_blank">{id}</a></td>
                            <td>{title}</td>
                            <td><span class="status-review">{state}</span></td>
                            <td>{assignee}</td>
                        </tr>
                        <!-- Additional rows -->
                    </tbody>
                </table>

                <h3>✅ Committed - Current & Multi-Semester ({count})</h3>
                <p>Epics with confirmed commitment (current semester or spanning multiple semesters).</p>
                <table class="work-item-table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Title</th>
                            <th>State</th>
                            <th>Commitment</th>
                            <th>Assignee</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td><a href="{azure_devops_url}" target="_blank">{id}</a></td>
                            <td>{title}</td>
                            <td><span class="status-committed">{state}</span></td>
                            <td>{commitment}</td>
                            <td>{assignee}</td>
                        </tr>
                        <!-- Additional rows -->
                    </tbody>
                </table>

                <h3>⏸️ Other ({count})</h3>
                <p>Epics that are deferred, rejected, or in other states.</p>
                <table class="work-item-table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Title</th>
                            <th>State</th>
                            <th>Commitment</th>
                            <th>Assignee</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td><a href="{azure_devops_url}" target="_blank">{id}</a></td>
                            <td>{title}</td>
                            <td><span class="status-other">{state}</span></td>
                            <td>{commitment}</td>
                            <td>{assignee}</td>
                        </tr>
                        <!-- Additional rows -->
                    </tbody>
                </table>
            </section>

            <div class="insights-box">
                <h2>💡 Planning Insights</h2>
                
                <h3>Capacity Considerations</h3>
                <ul>
                    <li><strong>Decision Required:</strong> {count} external dependencies + {count} under review epics = {total} items need decisions</li>
                    <li><strong>Active Commitments:</strong> {count} committed epics requiring resources</li>
                    <li><strong>Planning Risk:</strong> {High/Medium/Low} based on decision backlog</li>
                </ul>

                <h3>Key Focus Areas</h3>
                <ul>
                    <li><strong>{Area}:</strong> {count} items</li>
                    <!-- Additional focus areas -->
                </ul>

                <h3>Recommendations</h3>
                <ol>
                    <li><strong>Prioritize Decisions:</strong> {count} dependencies awaiting commitment</li>
                    <li><strong>Capacity Allocation:</strong> {count} committed epics need staffing</li>
                    <li><strong>Review Backlog:</strong> {count} under review items need evaluation</li>
                </ol>
            </div>
        </div>

        <div class="footer">
            <p><strong>Report Metadata:</strong></p>
            <p>Generated by: CloudInit Semester Planning Automation</p>
            <p>Data Source: Azure DevOps Project "One", Area Path "One\CloudInit"</p>
            <p>Data Types: External Dependencies (New/Under Review) + Internal Epics (All Commitment States)</p>
            <p>Excluded: Items with State="Done" (completed historical work)</p>
            <p>Last Fresh Data Pull: {date}</p>
        </div>
    </div>
</body>
</html>
```

---

## Section 7: Query Examples

### Example 1: Get External Dependencies (New/Under Review)

```python
# Step 1: Search for dependencies
dependency_search = mcp_azure-devops_search_workitem(
    project=["One"],
    searchText="PartnerAsk Producer AzLinux",
    top=100
)

# Step 2: Filter for CloudInit area
cloudinit_dep_ids = [
    item["fields"]["system.id"]
    for item in dependency_search["results"]
    if "CloudInit" in item["fields"].get("system.areapath", "")
]

# Step 3: Get full details
dependencies_full = mcp_azure-devops_wit_get_work_items_batch_by_ids(
    project="One",
    ids=cloudinit_dep_ids,
    fields=[
        "System.Id",
        "System.Title",
        "System.State",
        "System.AssignedTo",
        "Custom.CommitmentStatus"
    ]
)

# Step 4: Filter out Done (keep all commitment statuses)
planning_dependencies = [
    dep for dep in dependencies_full
    if dep["fields"].get("System.State") != "Done"
]

print(f"Found {len(planning_dependencies)} total dependencies")
```

### Example 2: Get Internal Epics by Commitment

```python
# Step 1: Search for Epics
epic_search = mcp_azure-devops_search_workitem(
    project=["One"],
    searchText="Epic",
    workItemType=["Epic"],
    top=100
)

# Step 2: Filter for CloudInit area
cloudinit_epic_ids = [
    item["fields"]["system.id"]
    for item in epic_search["results"]
    if "CloudInit" in item["fields"].get("system.areapath", "")
]

# Step 3: Get full details
epics_full = mcp_azure-devops_wit_get_work_items_batch_by_ids(
    project="One",
    ids=cloudinit_epic_ids,
    fields=[
        "System.Id",
        "System.Title",
        "System.State",
        "System.AssignedTo",
        "Custom.CommitmentStatus"
    ]
)

# Step 4: Filter out Done and categorize
active_epics = [
    epic for epic in epics_full
    if epic["fields"].get("System.State") != "Done"
]

# Categorize
under_review = [
    epic for epic in active_epics
    if epic["fields"].get("Custom.CommitmentStatus") in ["New", "Under Review"]
]

committed = [
    epic for epic in active_epics
    if "Committed" in epic["fields"].get("Custom.CommitmentStatus", "")
]

other = [
    epic for epic in active_epics
    if epic not in under_review and epic not in committed
]

print(f"Under Review: {len(under_review)}")
print(f"Committed: {len(committed)}")
print(f"Other: {len(other)}")
```

---

## Section 8: Planning Insights Generation

### Calculate Planning Metrics:

```python
# Total items requiring decisions
decision_items = len(planning_dependencies) + len(under_review_epics)

# Planning risk assessment
if decision_items > 10:
    risk_level = "High"
elif decision_items > 5:
    risk_level = "Medium"
else:
    risk_level = "Low"

# Capacity assessment
committed_items = len(committed_epics)
capacity_status = "Overcommitted" if committed_items > 15 else "Manageable"

# Focus area analysis (by tags)
focus_areas = {}
for item in planning_dependencies + active_epics:
    tags = item["fields"].get("System.Tags", "")
    # Parse tags and count by technology area
    # e.g., Security, Quality, GGAL, etc.
```

### Generate Recommendations:

```python
recommendations = []

if len(planning_dependencies) > 5:
    recommendations.append(
        f"High Priority: {len(planning_dependencies)} external dependencies "
        f"awaiting commitment decisions - schedule planning meeting"
    )

if len(under_review_epics) > 3:
    recommendations.append(
        f"Review Needed: {len(under_review_epics)} epics under review - "
        f"finalize commitment status for capacity planning"
    )

if len(committed_epics) > 10:
    recommendations.append(
        f"Capacity Warning: {len(committed_epics)} committed epics - "
        f"verify team capacity and consider deferring lower priority items"
    )
```

---

## Section 9: Report Quality Checklist

Before finalizing the planning report, verify:

- [ ] **Fresh data pull from Azure DevOps MCP server performed** for both dependencies and epics
- [ ] External dependencies filtered to New/Under Review status only
- [ ] All Done items excluded from both sources
- [ ] Epics categorized correctly: Under Review, Committed, Other
- [ ] All work item IDs are hyperlinked correctly
- [ ] Summary counts match table entries
- [ ] Both Markdown (.md) and HTML (.html) versions generated
- [ ] File naming follows convention with correct timestamp
- [ ] Planning insights include actionable recommendations
- [ ] Capacity considerations clearly stated
- [ ] Focus areas identified and quantified
- [ ] Risk assessment included (High/Medium/Low)

---

## Section 10: Best Practices

### Focus on Clarity:
- **Minimal Data:** Show only essential fields (ID, Title, Status, Assignee)
- **High-Level View:** Avoid detailed descriptions - link to Azure DevOps for details
- **Summary First:** Start with executive summary showing counts
- **Insights Over Data:** Provide planning recommendations, not just data dumps

### Planning-Specific Guidance:
- **Decision Focus:** Highlight items requiring commitment decisions
- **Capacity View:** Show committed work for resource planning
- **Risk Awareness:** Call out planning risks and overcommitment
- **Action Items:** Provide clear next steps for planning

### Data Freshness:
- **Always Fresh:** Never reuse cached data - query Azure DevOps each time
- **Timestamp:** Include generation timestamp in metadata
- **State Accuracy:** Exclude Done items to focus on active planning
- **Real-Time:** Report should reflect current state for planning decisions

---

## Section 11: Maintenance and Updates

### When to Generate Planning Report:
- **Pre-Semester Planning:** 2-3 weeks before semester starts
- **Mid-Semester Check:** Halfway through semester for adjustments
- **Planning Meetings:** Before capacity planning discussions
- **On-Demand:** When requested by leadership or PM

### Report Lifecycle:
- Keep planning reports with semester identifier
- Archive after semester completes
- Use for historical comparison of planning accuracy
- Track decision velocity (time from New to Committed)

---

## Revision History

| Date | Version | Changes | Author |
|---|---|---|---|
| 2025-10-09 | 1.0 | Initial semester planning report instructions created | Copilot |

---

**End of Instructions**
