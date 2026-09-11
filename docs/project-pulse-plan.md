# Project Pulse Dashboard Implementation Plan

## Objective

Build a dependency-free, responsive Project Pulse dashboard that presents project
name, owner, status, recent activity, and priority from JSON data. The dashboard
must run from the `app/` directory through the VS Code launch configuration and
must preserve the repository's existing exercise conventions.

## Scope and file assignments

| File | Owner | Responsibilities |
| --- | --- | --- |
| `app/index.html` | Coder | Create semantic dashboard markup, load `project-data.json`, render one project card per record, and provide loading, empty, and error states. |
| `app/styles.css` | Designer | Define the responsive visual system, layout, cards, typography, status and priority treatments, focus states, spacing, contrast, and mobile behavior. |
| `app/project-data.json` | Coder | Provide valid JSON with a top-level `projects` array and complete records containing `name`, `owner`, `status`, `recentActivity`, and `priority`. |
| `.vscode/launch.json` | Coder | Configure the `Run Project Pulse Dashboard` launch target to serve `app/` with `python3 -m http.server 5500` and open `index.html`. |
| `docs/project-pulse-plan.md` | Planner | Define the implementation sequence, ownership, dependencies, parallel work, risks, and validation expectations. |

### Designer responsibilities

- Establish the visual hierarchy for the dashboard and project cards.
- Provide stable selectors for the Coder, including `.dashboard` and
  `.project-card`.
- Make status and priority scannable without relying on color alone.
- Include responsive behavior for narrow and wide viewports.
- Include visible keyboard focus states and readable contrast.
- Review the integrated page for layout, accessibility, and visual consistency.

### Coder responsibilities

- Implement semantic, accessible HTML using the Designer's selectors.
- Use the exact JSON field names and render all project records dynamically.
- Include visible loading, empty, and error handling.
- Create realistic sample data with multiple projects.
- Configure the VS Code launch target with strict, valid JSON.
- Confirm that the page works when served over HTTP rather than opened directly
  from the filesystem.

### Coordination contract

The Designer owns only `app/styles.css`; the Coder owns the other three
implementation files. The Coder must use the Designer's agreed selectors and
the JSON schema without duplicating or hard-coding project cards. Any required
change to another role's file is returned to that role. The Orchestrator
coordinates handoffs and performs the final cross-file review.

## Implementation phases

### 1. Confirm repository contracts

**Owner:** Planner/Orchestrator  
**Dependencies:** None

Review the project brief, agent instructions, existing workflow, and
`scripts/validate-exercise.sh`. Confirm the required output files, exact launch
name, data fields, selectors, and existing validation commands. Do not modify
unrelated repository files.

**Exit criteria:** ownership is non-overlapping, the HTML/CSS/JSON contract is
explicit, and the validation approach is known.

### 2. Implement independent support files in parallel

**Dependencies:** Phase 1

#### 2a. Design stylesheet

**Owner:** Designer  
**File:** `app/styles.css`

Implement `.dashboard` and `.project-card`, a responsive grid, card
`border-radius`, `box-shadow`, readable typography, status and priority
indicators, and accessible focus/contrast states.

**Exit criteria:** the stylesheet has the required selectors and visual
behavior at mobile and desktop widths.

#### 2b. Create project data

**Owner:** Coder  
**File:** `app/project-data.json`

Create a top-level `projects` array with multiple records. Every record must
contain non-empty `name`, `owner`, `status`, `recentActivity`, and `priority`
values. Keep `recentActivity` camel-cased exactly as specified.

**Exit criteria:** the file parses as JSON and every record has all required
fields.

#### 2c. Configure local preview

**Owner:** Coder  
**File:** `.vscode/launch.json`

Create strict JSON containing a configuration named exactly
`Run Project Pulse Dashboard`. It must serve from `${workspaceFolder}/app`,
run `python3 -m http.server 5500`, include a `serverReadyAction`, and open
`http://localhost:%s/index.html`.

**Exit criteria:** the file parses as strict JSON and the launch configuration
matches the required command, directory, name, and URL.

### 3. Implement the dashboard page

**Owner:** Coder  
**File:** `app/index.html`  
**Dependencies:** Phase 2a, 2b, and 2c

Create a page with the exact visible title or heading `Project Pulse`. Link
`styles.css`, load `project-data.json`, and render one `.project-card` for
each project. Each card must show the name, owner, status, recent activity, and
priority. Use semantic landmarks and labels, and provide visible loading,
empty, and fetch/parse error states. Keep the implementation dependency-free;
because no separate JavaScript file is requested, small rendering logic may
remain in the HTML page.

**Exit criteria:** the page renders data-driven cards and remains useful when
data is loading, empty, malformed, or unavailable.

### 4. Integrate and review

**Owner:** Orchestrator  
**Dependencies:** Phase 3

Review all four implementation files together. Confirm HTML selectors match the
stylesheet, HTML field access matches JSON, card count is data-driven, and the
launch target serves the same `app/` directory. Route corrections to the
responsible Designer or Coder rather than taking ownership of their files.

**Exit criteria:** the four files form one runnable dashboard and no unrelated
files changed.

## Dependencies

| Work item | Depends on | Reason |
| --- | --- | --- |
| Contract confirmation | Repository inspection | The plan must reflect the repository's existing brief and scripts. |
| Stylesheet | Selector and content contract | CSS needs stable HTML hooks and known content categories. |
| JSON data | Project brief | The brief defines the required schema and fields. |
| Launch configuration | App path and entry filename | The server must serve `app/` and open `index.html`. |
| Dashboard HTML | Stylesheet hooks and JSON schema | Markup must use the agreed selectors and render exact field names. |
| Integrated review | All four implementation files | Cross-file wiring cannot be validated in isolation. |

## Parallel work decisions

After contract confirmation, the Designer may work on `app/styles.css` in
parallel with the Coder creating `app/project-data.json` and
`.vscode/launch.json`; these tasks have separate ownership and no file overlap.

`app/index.html` is intentionally sequenced after the CSS selector contract and
JSON schema are fixed, so the page does not drift from either. Integrated
review, server validation, and browser validation must run after all four files
exist. Corrections remain sequential by file owner to avoid conflicting edits.

## Validation expectations

Run the repository's existing validation first:

```bash
bash scripts/validate-exercise.sh
```

Validate both JSON files:

```bash
python3 -m json.tool app/project-data.json >/dev/null
python3 -m json.tool .vscode/launch.json >/dev/null
```

Check required contracts:

```bash
grep -Fq 'Project Pulse' app/index.html
grep -Fq 'styles.css' app/index.html
grep -Fq 'project-data.json' app/index.html
grep -Fq 'project-card' app/index.html
grep -Fq '.dashboard' app/styles.css
grep -Fq '.project-card' app/styles.css
grep -Fq 'border-radius' app/styles.css
grep -Fq 'box-shadow' app/styles.css
grep -Fq '"projects"' app/project-data.json
grep -Fq 'Run Project Pulse Dashboard' .vscode/launch.json
grep -Fq 'index.html' .vscode/launch.json
```

Run the configured server from `app/` and verify both resources:

```bash
(cd app && python3 -m http.server 5500 --bind 127.0.0.1)
curl --fail http://127.0.0.1:5500/index.html >/dev/null
curl --fail http://127.0.0.1:5500/project-data.json >/dev/null
```

Use VS Code's **Run and Debug** action to select `Run Project Pulse Dashboard`.
Confirm that the browser opens `/index.html` rather than a directory listing,
the heading and multiple cards are visible, and every card displays owner,
status, recent activity, and priority.

Perform a manual accessibility and responsive review: verify a clear `h1`,
logical landmarks, readable contrast, visible keyboard focus, text-based status
and priority indicators, no horizontal scrolling on narrow screens, and
understandable loading, empty, and error states.

## Risks and assumptions

- `fetch()` should be validated through the local HTTP server; direct
  `file://` opening may be blocked by browser security rules.
- Malformed or unavailable JSON must produce a visible error, not a silent
  empty dashboard.
- An empty `projects` array must produce a useful empty state.
- Missing fields should be validated or rendered with explicit fallback text,
  never `undefined`.
- `.vscode/launch.json` must remain strict JSON without comments.
- Port 5500 is part of the launch contract; report a port conflict rather than
  silently changing the configured port.
- Do not alter agent definitions, workflows, scripts, devcontainer files, or
  unrelated documentation.

## Final acceptance criteria

1. All four assigned implementation files exist and are valid.
2. `app/index.html` shows `Project Pulse`, references the CSS and JSON, and
   renders data-driven `.project-card` elements.
3. Cards display name, owner, status, recent activity, and priority.
4. `app/styles.css` provides responsive, accessible styling with the required
   selectors, rounded cards, and depth.
5. `app/project-data.json` contains a valid, complete top-level `projects`
   array.
6. `.vscode/launch.json` contains the exact launch name, server command,
   working directory, and `/index.html` URL.
7. Existing validation, JSON checks, server checks, and manual browser review
   pass.
