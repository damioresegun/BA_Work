# Install Obsidian Plugins
- Templater
- Dataview
- Tasks
- QuickAdd
- Kanban
- Better Word Count
- Omnisearch
- Recent Files
- Day Planner
- Periodic Notes
# Prepare your Obsidian
1. Temporarily disable:
```
Settings → Templater → Trigger Templater on new file creation
```
2. Create this file and folder in the vault:
```
Templates/00 - Setup Project Vault.md
```
3. Copy the below into a new note and save it as `Setup.md`
```
<%*
const promptedName = await tp.system.prompt("Enter the project name");

if (!promptedName || !promptedName.trim()) {
  new Notice("Setup cancelled: no project name was provided.");
  return;
}

const projectName = promptedName.trim();

if (/[<>:"/\\|?*]/.test(projectName)) {
  new Notice('Invalid project name. Avoid: < > : " / \\ | ? *');
  return;
}

const root = projectName;
const today = tp.date.now("YYYY-MM-DD");

const tag = (expression) =>
  "<" + "% " + expression + " %" + ">";

const D = tag('tp.date.now("YYYY-MM-DD")');
const TIME = tag('tp.date.now("HH:mm")');
const TITLE = tag("tp.file.title");

const kanbanPath =
  `${root}/02 - Product and Requirements/Backlog Views/User Story Kanban.md`;

const USER_STORY_AUTOMATION = [
  "<" + "%*",
  `const boardPath = ${JSON.stringify(kanbanPath)};`,
  "const board = app.vault.getAbstractFileByPath(boardPath);",
  "",
  "if (!board) {",
  '  new Notice("User Story Kanban board was not found.");',
  "} else {",
  "  const boardContent = await app.vault.read(board);",
  "  const storyTitle = tp.file.title;",
  '  const storyPath = tp.file.path(true).replace(/\\.md$/, "");',
  '  const card = "- [[" + storyPath + "|" + storyTitle + "]]";',
  "",
  "  if (!boardContent.includes(card)) {",
  "    const heading = /^## Developing\\s*\\n/m;",
  "",
  "    if (heading.test(boardContent)) {",
  "      const updatedBoard = boardContent.replace(",
  "        heading,",
  '        match => match + card + "\\n"',
  "      );",
  "",
  "      await app.vault.modify(board, updatedBoard);",
  '      new Notice("User story added to Developing.");',
  "    } else {",
  '      new Notice("The Developing column was not found.");',
  "    }",
  "  }",
  "}",
  "%" + ">"
].join("\n");

const folders = [
  "00 - Home",
  "00 - Home/Daily Notes",

  "01 - Discovery and Analysis",
  "01 - Discovery and Analysis/Analysis",
  "01 - Discovery and Analysis/Stakeholders",
  "01 - Discovery and Analysis/Personas",
  "01 - Discovery and Analysis/User Journeys",
  "01 - Discovery and Analysis/Business Processes",
  "01 - Discovery and Analysis/Use Cases",
  "01 - Discovery and Analysis/Research",
  "01 - Discovery and Analysis/Workshop Notes",

  "02 - Product and Requirements",
  "02 - Product and Requirements/Product Vision",
  "02 - Product and Requirements/Epics",
  "02 - Product and Requirements/Features",
  "02 - Product and Requirements/User Stories",
  "02 - Product and Requirements/Non-Functional Requirements",
  "02 - Product and Requirements/Acceptance Criteria",
  "02 - Product and Requirements/Backlog Views",

  "03 - UX and Design",
  "03 - UX and Design/Design Briefs",
  "03 - UX and Design/Wireframes",
  "03 - UX and Design/Prototypes",
  "03 - UX and Design/Usability Tests",
  "03 - UX and Design/UX Decisions",

  "04 - Architecture and Engineering",
  "04 - Architecture and Engineering/Architecture Overview",
  "04 - Architecture and Engineering/Architecture Decisions",
  "04 - Architecture and Engineering/Technical Spikes",
  "04 - Architecture and Engineering/Integrations",
  "04 - Architecture and Engineering/Technical Constraints",
  "04 - Architecture and Engineering/Engineering Notes",

  "05 - Delivery Support",
  "05 - Delivery Support/Sprint References",
  "05 - Delivery Support/PI References",
  "05 - Delivery Support/Release Planning",
  "05 - Delivery Support/Demonstrations",
  "05 - Delivery Support/Retrospective Actions",

  "06 - Governance and Traceability",
  "06 - Governance and Traceability/Decisions",
  "06 - Governance and Traceability/Risks",
  "06 - Governance and Traceability/Assumptions",
  "06 - Governance and Traceability/Issues",
  "06 - Governance and Traceability/Dependencies",
  "06 - Governance and Traceability/Change Requests",
  "06 - Governance and Traceability/Traceability",

  "07 - Meetings",
  "07 - Meetings/Stakeholder Meetings",
  "07 - Meetings/Team Meetings",
  "07 - Meetings/Workshops",
  "07 - Meetings/Refinement Sessions",
  "07 - Meetings/Decision Meetings",

  "08 - Reference",
  "08 - Reference/Glossary",
  "08 - Reference/Standards",
  "08 - Reference/Product References",
  "08 - Reference/External Links",

  "09 - Reports",
  "09 - Reports/Status Reports",
  "09 - Reports/Requirement Reports",
  "09 - Reports/Risk Reports",
  "09 - Reports/Release Reports",

  "Templates",
  "Attachments"
];

async function ensureFolder(path) {
  if (!app.vault.getAbstractFileByPath(path)) {
    await app.vault.createFolder(path);
  }
}

async function ensureFile(path, content) {
  if (!app.vault.getAbstractFileByPath(path)) {
    await app.vault.create(path, content);
  }
}

for (const folder of folders) {
  await ensureFolder(`${root}/${folder}`);
}

await ensureFile(
  `${root}/02 - Product and Requirements/Backlog Views/User Story Kanban.md`,
  `---
kanban-plugin: board
---

## Developing

---

## Ready for refinement

---

## Refinement 1

---

## Refinement 2

---

## For estimation

---

## Done
`
);

await ensureFile(
  `${root}/00 - Home/Project Overview.md`,
  `---
project: ${projectName}
type: project-overview
status: Active
product_owner:
scrum_master:
business_analyst:
ux_designer:
software_architect:
developers: []
created: ${today}
updated: ${today}
tags: [project, overview]
---

# ${projectName} — Project Overview

## Purpose

## Business problem

## Objectives

## Scope

### In scope

- 

### Out of scope

- 

## Team

| Role | Person |
|---|---|
| Scrum Master | |
| Product Owner | |
| Business Analyst | |
| UX Designer | |
| Software Architect | |
| Developer | |
| Developer | |
| Developer | |
| Developer | |

## Delivery information

- Agile planning tool:
- Current PI:
- Current release:
- Project start date:

## Key links

- 
`
);

await ensureFile(
  `${root}/00 - Home/Status Models.md`,
  `---
project: ${projectName}
type: reference
reference_area: status-models
tags: [reference, status-models]
---

# ${projectName} — Status Models

## User Stories

| Status | Meaning |
|---|---|
| Developing | Story is being analysed or drafted |
| Ready for refinement | Story is prepared for refinement |
| Refinement 1 | First refinement session |
| Refinement 2 | Further clarification is required |
| For estimation | Story is ready for estimation |
| Done | Refinement and estimation are complete |

## Decisions

Proposed, Under Discussion, Approved, Rejected, Superseded.

## Architecture decisions

Proposed, Under Review, Accepted, Rejected, Superseded.

## Risks

Open, Monitoring, Mitigating, Escalated, Accepted, Closed.

## Dependencies

Identified, Under Review, Active, At Risk, Resolved, Cancelled.

## Assumptions

Open, Validating, Confirmed, Rejected, Converted to Risk.

## Meetings and workshops

Planned, Held, Follow-up Required, Closed, Cancelled.

## Stakeholders

Identified, Engaging, Monitoring, Inactive, Archived.

## Parking lot items

New, Reviewing, Converted, Deferred, Dismissed, Done.
`
);

await ensureFile(
  `${root}/00 - Home/Installation Guide.md`,
  `---
project: ${projectName}
type: installation-guide
tags: [reference, installation]
---

# ${projectName} — Installation Guide

## Purpose

This note explains how to configure the Obsidian plugins used by this project vault.

## Required plugins

### Templater

Purpose:

- Creates notes from templates
- Generates dates and titles
- Runs the project setup script

Configuration:

1. Open \`Settings → Templater\`.
2. Set the template folder to:
   \`${root}/Templates\`
3. Keep automatic processing on new-file creation disabled while using the setup script.
4. Use \`Templater: Create new note from template\` to run templates explicitly.

### Dataview

Purpose:

- Generates dashboards
- Lists features, stories, risks, decisions, and parking-lot items

Configuration:

1. Install and enable Dataview.
2. Enable JavaScript queries only if you later add DataviewJS queries.
3. The dashboard uses standard Dataview queries.

### Tasks

Purpose:

- Tracks selected tasks across the project

Use \`#todo\` only on tasks that should appear on the central task list.

Example:

- [ ] Confirm acceptance criteria #todo

The dashboard should query:

\`\`\`tasks
not done
tags include #todo
path includes ${root}
sort by due
\`\`\`

### QuickAdd

Purpose:

- Creates project notes quickly from templates

Create Template choices using the mappings below.

| Choice | Template | Destination |
|---|---|---|
| New Analysis | \`${root}/Templates/Analysis.md\` | \`${root}/01 - Discovery and Analysis/Analysis\` |
| New Architecture Decision | \`${root}/Templates/Architecture Decision.md\` | \`${root}/04 - Architecture and Engineering/Architecture Decisions\` |
| New Assumption | \`${root}/Templates/Assumption.md\` | \`${root}/06 - Governance and Traceability/Assumptions\` |
| New Decision | \`${root}/Templates/Decision.md\` | \`${root}/06 - Governance and Traceability/Decisions\` |
| New Dependency | \`${root}/Templates/Dependency.md\` | \`${root}/06 - Governance and Traceability/Dependencies\` |
| New Feature | \`${root}/Templates/Feature.md\` | \`${root}/02 - Product and Requirements/Features\` |
| New Glossary Term | \`${root}/Templates/Glossary Term.md\` | \`${root}/08 - Reference/Glossary\` |
| New Meeting | \`${root}/Templates/Meeting.md\` | \`${root}/07 - Meetings/Stakeholder Meetings\` |
| New Parking Lot Item | \`${root}/Templates/Parking Lot.md\` | \`${root}/00 - Home\` |
| New Risk | \`${root}/Templates/Risk.md\` | \`${root}/06 - Governance and Traceability/Risks\` |
| New Stakeholder | \`${root}/Templates/Stakeholder.md\` | \`${root}/01 - Discovery and Analysis/Stakeholders\` |
| New User Story | \`${root}/Templates/User Story.md\` | \`${root}/02 - Product and Requirements/User Stories\` |
| New Workshop Note | \`${root}/Templates/Workshop Note.md\` | \`${root}/07 - Meetings/Workshops\` |
| New Daily Note | \`${root}/Templates/Daily Note.md\` | \`${root}/00 - Home/Daily Notes\` |

After creating the choices, create a choice group named \`${projectName} — New Note\`.

Add all note choices to that group.

Recommended hotkeys:

- New Note menu: \`Ctrl/Cmd + Alt + N\`
- Parking lot item: \`Ctrl/Cmd + Alt + P\`

### Kanban

Purpose:

- Displays User Stories by refinement status
- Adds new User Stories to the \`Developing\` column

Open:

\`${root}/02 - Product and Requirements/Backlog Views/User Story Kanban.md\`

The User Story template automatically adds new stories to the first column.

Kanban movement does not automatically update the YAML \`status\` property. Keep the card and status property aligned manually.

## Optional plugins

### Calendar

Useful for navigating daily notes.

### Metadata Menu

Useful for selecting controlled values such as User Story status.

Configure the User Story status values as:

- Developing
- Ready for refinement
- Refinement 1
- Refinement 2
- For estimation
- Done

### Excalidraw

Useful for process maps, journeys, architecture sketches, and workshop diagrams.

### Linter

Useful later for enforcing consistent Markdown and YAML formatting.

## Recommended workflow

1. Capture ideas using the Parking Lot template.
2. Analyse them using the Analysis template.
3. Convert validated findings into Features and User Stories.
4. Refine User Stories using the Kanban board.
5. Record decisions, assumptions, risks, and dependencies separately.
6. Use \`#todo\` only for tasks requiring central follow-up.
7. Keep sprint and PI execution in the team Agile tool.
`
);

const templates = {
  "Analysis.md": `---
project: ${projectName}
type: analysis
status: Under Analysis
analysis_id:
analyst:
analysis_area:
related_features: []
related_user_stories: []
related_processes: []
related_stakeholders: []
created: ${D}
updated: ${D}
tags: [analysis]
---

# Analysis: ${TITLE}

## Purpose

## Problem or question

## Background and context

## Scope

### In scope

- 

### Out of scope

- 

## Evidence and sources

- 

## Findings

### Finding 1

**Observation:**

**Evidence:**

**Implication:**

## Recommendation

## Impact assessment

- Business:
- User:
- Process:
- UX:
- Technical:
- Delivery:
- Data:

## Risks

- 

## Assumptions

- 

## Dependencies

- 

## Open questions

- [ ] Question — owner — due date #todo

## Conclusions

## Follow-up actions

- [ ] Action — owner — due date #todo

## Related notes

- 
`,

  "Architecture Decision.md": `---
project: ${projectName}
type: architecture-decision
status: Proposed
decision_owner:
decision_date:
decision_area:
related_features: []
related_user_stories: []
related_risks: []
created: ${D}
updated: ${D}
tags: [architecture, decision]
---

# Architecture Decision: ${TITLE}

## Context

## Decision drivers

- 

## Options considered

### Option 1

**Advantages:**

- 

**Disadvantages:**

- 

### Option 2

**Advantages:**

- 

**Disadvantages:**

- 

## Decision

## Rationale

## Consequences

### Positive

- 

### Negative or trade-offs

- 

## Business impact

## Technical impact

## Follow-up actions

- [ ] Action — owner — due date #todo

## Related notes

- 
`,

  "Assumption.md": `---
project: ${projectName}
type: assumption
status: Open
owner:
validation_due:
impact_if_false: Medium
related_features: []
related_user_stories: []
related_risks: []
created: ${D}
updated: ${D}
tags: [assumption]
---

# Assumption: ${TITLE}

## Assumption statement

We assume that **[state the assumption clearly]**.

## Why this assumption is being made

## Evidence or source

- 

## Impact if incorrect

- Business:
- User:
- Technical:
- Delivery:

## Validation

- Method:
- Owner:
- Due date:

## Outcome

- Result:
- Date:
- Evidence:

## Follow-up

- [ ] Validate assumption — owner — due date #todo

## Related notes

- 
`,

  "Decision.md": `---
project: ${projectName}
type: decision
status: Proposed
decision_owner:
decision_date:
decision_area:
requested_by:
related_features: []
related_user_stories: []
related_risks: []
related_dependencies: []
created: ${D}
updated: ${D}
tags: [decision]
---

# Decision: ${TITLE}

## Context

## Decision required

## Options considered

1. 
2. 
3. 

## Evaluation criteria

- 

## Recommendation

## Decision

## Rationale

## Consequences

### Positive

- 

### Negative or trade-offs

- 

## Follow-up actions

- [ ] Action — owner — due date #todo

## Related notes

- 
`,

  "Dependency.md": `---
project: ${projectName}
type: dependency
status: Identified
owner:
dependency_type:
source_team:
dependent_team:
needed_by:
related_features: []
related_user_stories: []
related_pi:
related_sprint:
created: ${D}
updated: ${D}
tags: [dependency]
---

# Dependency: ${TITLE}

## Dependency statement

${projectName} depends on **[party, system, decision, or deliverable]** for **[required outcome]**.

## Required outcome

## Impact if delayed

- Scope:
- Schedule:
- Quality:
- Cost:
- Users or stakeholders:

## Mitigation or contingency

## Follow-up actions

- [ ] Confirm owner — owner — due date #todo
- [ ] Confirm delivery date — owner — due date #todo

## Resolution

- Resolution:
- Resolved date:
- Evidence:

## Related notes

- 
`,

  "Feature.md": `---
project: ${projectName}
type: feature
status: Draft
product_owner:
analyst:
priority: Medium
target_release:
pi:
business_value:
related_epic:
created: ${D}
updated: ${D}
tags: [feature]
---

# Feature: ${TITLE}

## Problem or opportunity

## Business value

## Scope

### In scope

- 

### Out of scope

- 

## User stories

\`\`\`dataview
LIST
FROM "${root}/02 - Product and Requirements/User Stories"
WHERE feature = this.file.link
\`\`\`

## UX considerations

## Technical considerations

## Dependencies

## Risks

## Acceptance criteria

- [ ] 

## Open questions

- [ ] Question — owner — due date #todo

## Related notes

- 
`,

  "Glossary Term.md": `---
project: ${projectName}
type: glossary-term
status: Active
term:
definition:
category:
acronym:
source:
created: ${D}
updated: ${D}
tags: [glossary]
---

# Glossary: ${TITLE}

## Definition

## Context

## Alternative names

- 

## Acronym or abbreviation

- 

## Business meaning

## Technical meaning

## Examples

- 

## Source

- 

## Related terms

- 
`,

  "Meeting.md": `---
project: ${projectName}
type: meeting
status: Planned
meeting_type:
date: ${D}
facilitator:
note_taker:
attendees: []
related_features: []
related_user_stories: []
related_decisions: []
related_risks: []
created: ${D}
updated: ${D}
tags: [meeting]
---

# Meeting: ${TITLE}

## Meeting details

- Date:
- Meeting type:
- Facilitator:
- Note taker:
- Location or link:

## Purpose

## Agenda

1. 
2. 
3. 

## Discussion

## Decisions

- 

## Actions

- [ ] Action — owner — due date #todo

## Risks and issues

- 

## Open questions

- [ ] Question — owner — due date #todo

## Follow-up

- Date:
- Purpose:

## Notes to create or update

- [ ] Create or update decision #todo
- [ ] Create or update risk #todo
- [ ] Create or update dependency #todo
- [ ] Update feature or user story #todo
`,

  "Parking Lot.md": `---
project: ${projectName}
type: parking-lot
status: New
capture_date: ${D}
capture_time: ${TIME}
source:
review_date:
potential_type:
created: ${D}
updated: ${D}
tags: [parking-lot]
---

# Parking Lot: ${TITLE}

## Quick capture

## Why it may matter

- 

## Possible follow-up

- [ ] Feature
- [ ] User story
- [ ] Requirement
- [ ] Decision
- [ ] Risk
- [ ] Assumption
- [ ] Dependency
- [ ] Workshop topic
- [ ] Glossary term
- [ ] Reference note
- [ ] No further action

## Initial context

- Source:
- Related person or team:
- Related meeting:
- Related feature or process:

## Review

- Status: New
- Review date:
- Review outcome:

## Related notes

- 
`,

  "Risk.md": `---
project: ${projectName}
type: risk
status: Open
risk_owner:
probability: Medium
impact: Medium
target_date:
related_features: []
related_user_stories: []
related_dependencies: []
created: ${D}
updated: ${D}
tags: [risk]
---

# Risk: ${TITLE}

## Risk statement

There is a risk that **[uncertain event]** may occur, resulting in **[consequence]**.

## Cause

## Potential consequences

- Business:
- User:
- Technical:
- Delivery:
- Quality:
- Compliance or security:

## Assessment

- Probability:
- Impact:
- Overall rating:

## Preventive actions

- [ ] Action — owner — due date #todo

## Mitigation plan

## Trigger

## Escalation

- Escalation owner:
- Escalation threshold:

## Closure

- Closure reason:
- Closed date:

## Related notes

- 
`,

  "Stakeholder.md": `---
project: ${projectName}
type: stakeholder
status: Identified
name:
role:
organization:
influence: Medium
interest: Medium
engagement_strategy:
relationship_owner:
related_features: []
related_processes: []
created: ${D}
updated: ${D}
tags: [stakeholder]
---

# Stakeholder: ${TITLE}

## Details

- Name or group:
- Role:
- Organization or team:
- Relationship owner:

## Goals and interests

- 

## Concerns or pain points

- 

## Information needs

- 

## Decisions and approvals

- 

## Engagement strategy

## Engagement history

| Date | Interaction | Outcome | Follow-up |
|---|---|---|---|
|  |  |  |  |

## Open actions

- [ ] Action — owner — due date #todo

## Related notes

- 
`,

  "User Story.md": `---
project: ${projectName}
type: user-story
status: Developing
feature:
epic:
analyst:
product_owner:
pi:
sprint:
priority: Medium
story_points:
created: ${D}
updated: ${D}
tags: [user-story]
---

${USER_STORY_AUTOMATION}

# User Story: ${TITLE}

## Story

As a **[user/persona]**,  
I want **[capability]**,  
so that **[business value]**.

## Acceptance criteria

- Given
- When
- Then

## Business rules

- 

## UX considerations

- 

## Technical considerations

- 

## Dependencies

- 

## Refinement workflow

- [ ] Business value and user are clear
- [ ] Acceptance criteria are testable
- [ ] Story moved to \`Ready for refinement\`
- [ ] Refinement 1 completed
- [ ] Refinement 2 completed if required
- [ ] UX input completed or confirmed unnecessary
- [ ] Architecture input completed or confirmed unnecessary
- [ ] Story moved to \`For estimation\`
- [ ] Estimate recorded
- [ ] Story moved to \`Done\`

## Open questions

- [ ] Question — owner — due date #todo

## Kanban board

[[User Story Kanban]]

## Related notes

- 
`,

  "Workshop Note.md": `---
project: ${projectName}
type: workshop
status: Planned
workshop_type:
date: ${D}
facilitator:
note_taker:
participants: []
objective:
created: ${D}
updated: ${D}
tags: [workshop, analysis]
---

# Workshop: ${TITLE}

## Objective

## Participants

- 

## Preparation

- [ ] Confirm objective
- [ ] Prepare agenda
- [ ] Confirm participants

## Agenda

1. 
2. 
3. 

## Current-state understanding

## Observations and insights

- 

## Requirements identified

- 

## Pain points

- 

## Opportunities

- 

## Decisions made

- 

## Assumptions raised

- 

## Risks raised

- 

## Open questions

- [ ] Question — owner — due date #todo

## Actions

- [ ] Action — owner — due date #todo

## Outputs to create or update

- [ ] Analysis
- [ ] Feature
- [ ] User story
- [ ] Decision
- [ ] Risk
- [ ] Assumption
- [ ] Dependency

## Related notes

- 
`,

  "Daily Note.md": `---
project: ${projectName}
type: daily-note
date: ${D}
status: Open
created: ${D}
updated: ${D}
tags: [daily, journal]
---

# Daily Note: ${D}

## Day plan

- [ ] 09:00 
- [ ] 10:00 
- [ ] 11:00 
- [ ] 12:00 
- [ ] 13:00 
- [ ] 14:00 
- [ ] 15:00 
- [ ] 16:00 
- [ ] 17:00 
- [ ] 18:00 

## Top priorities

1. 
2. 
3. 

## Meetings and appointments

- 

## Notes and journal

## Decisions

- 

## Risks, blockers, or concerns

- 

## Follow-up actions

- [ ] Action — owner — due date #todo

## End-of-day review

### Completed

- 

### Not completed

- 

### Carry forward

- [ ] 

### Reflection

## Related notes

- 
`
};

for (const [name, content] of Object.entries(templates)) {
  await ensureFile(`${root}/Templates/${name}`, content);
}

const dashboard = `---
project: ${projectName}
type: dashboard
---

# ${projectName} Dashboard

- [[Project Overview]]
- [[Installation Guide]]
- [[Status Models]]
- [[User Story Kanban]]

## Analysis work

\`\`\`dataview
TABLE status, analyst, analysis_area, file.mtime AS "Updated"
FROM "${root}/01 - Discovery and Analysis/Analysis"
WHERE project = "${projectName}"
SORT file.mtime DESC
\`\`\`

## User stories by status

\`\`\`dataview
TABLE status, feature, priority, analyst, product_owner
FROM "${root}/02 - Product and Requirements/User Stories"
WHERE project = "${projectName}"
SORT status ASC, file.name ASC
\`\`\`

## Open governance items

\`\`\`dataview
TABLE type, status, risk_owner, owner, target_date, needed_by
FROM "${root}/06 - Governance and Traceability"
WHERE project = "${projectName}"
  AND status != "Closed"
  AND status != "Resolved"
  AND status != "Cancelled"
SORT target_date ASC
\`\`\`

## Parking lot

\`\`\`dataview
TABLE status, capture_date, potential_type, source
FROM "${root}/00 - Home"
WHERE type = "parking-lot"
  AND status != "Done"
  AND status != "Dismissed"
SORT capture_date DESC
\`\`\`

## Tracked todo items

\`\`\`tasks
not done
tags include #todo
path includes ${root}
sort by due
\`\`\`

## Folder guides

\`\`\`dataview
LIST
FROM "${root}"
WHERE type = "folder-guide"
SORT file.path ASC
\`\`\`
`;

await ensureFile(`${root}/00 - Home/${projectName} Dashboard.md`, dashboard);

const guideBodies = {
  "00 - Home": `# 00 - Home

Contains the project overview, dashboard, status models, installation guide, daily notes, and parking lot.

Flow:

Capture quickly → Review parking lot → Convert into a formal note → Link related information.`,

  "01 - Discovery and Analysis": `# 01 - Discovery and Analysis

Contains analysis, stakeholders, personas, journeys, processes, use cases, research, and workshop notes.

Flow:

Explore → Gather evidence → Analyse → Identify findings → Update requirements, decisions, risks, or dependencies.`,

  "02 - Product and Requirements": `# 02 - Product and Requirements

Contains product vision, epics, features, user stories, non-functional requirements, acceptance criteria, and backlog views.

User-story flow:

Developing → Ready for refinement → Refinement 1 → Refinement 2 → For estimation → Done.`,

  "03 - UX and Design": `# 03 - UX and Design

Contains design briefs, wireframes, prototypes, usability tests, and UX decisions.

Flow:

User need → Journey → Design brief → Prototype → Usability validation → Updated requirement.`,

  "04 - Architecture and Engineering": `# 04 - Architecture and Engineering

Contains architecture overviews, architecture decisions, technical spikes, integrations, constraints, and engineering notes.

Flow:

Requirement → Investigation → Options → Architecture decision → Implementation.`,

  "05 - Delivery Support": `# 05 - Delivery Support

Contains lightweight references to sprints, PIs, releases, demonstrations, and retrospective actions.

The Agile planning tool remains the source of truth for delivery execution.`,

  "06 - Governance and Traceability": `# 06 - Governance and Traceability

Contains decisions, risks, assumptions, issues, dependencies, change requests, and traceability.

Flow:

Identify → Record → Assign owner → Analyse impact → Agree action → Track to closure.`,

  "07 - Meetings": `# 07 - Meetings

Contains stakeholder meetings, team meetings, workshops, refinement sessions, and decision meetings.

Flow:

Prepare → Capture → Extract decisions and actions → Update related notes.`,

  "08 - Reference": `# 08 - Reference

Contains glossary terms, standards, product references, and external links.`,

  "09 - Reports": `# 09 - Reports

Contains status, requirement, risk, and release reporting views.`,

  "Templates": `# Templates

Contains the reusable templates for this project.

Do not store completed project notes here.`,

  "Attachments": `# Attachments

Contains approved supporting files linked from project notes.

Use meaningful filenames and follow applicable information-handling rules.`
};

for (const [folder, body] of Object.entries(guideBodies)) {
  const folderPrefixMatch = folder.match(/^(\d{2})\s*-\s*/);

  const guideFileName = folderPrefixMatch
    ? `${folderPrefixMatch[1]} - Folder Guide.md`
    : `${folder} - Folder Guide.md`;

  await ensureFile(
    `${root}/${folder}/${guideFileName}`,
    `---
project: ${projectName}
type: folder-guide
folder: ${folder}
created: ${today}
updated: ${today}
tags: [folder-guide]
---

${body}
`
  );
}


new Notice(`${projectName} project vault created successfully.`);
%>

```
3. Run and select the new note with:
```
  Templater: Create new note from template
```
# Setup QuickAdd
4. Open:
```
Settings → QuickAdd → Manage choices
```
5. Create a Template choice for each template in `<Project Name>/00 - Home/Installation Guide.md`
6. Set the destination folder for each choice
7. Create a choice group `<Project Name> — New Note`
8. Add the individual choices to that group ensuring that there is a New User Story:
| Choice | Template | Destination |
|---|---|---|
| New User Story | `<Project Name>/Templates/User Story.md` | `<Project Name>/02 - Product and Requirements/User Stories` |
9. Set:
```
Date format: YYYY-MM-DD
New file location: <Project Name>/00 - Home/Daily Notes
Template file: <Project Name>/Templates/Daily Note.md
```
