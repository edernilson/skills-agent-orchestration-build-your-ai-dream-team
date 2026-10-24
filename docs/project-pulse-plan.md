# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona’s Project Pulse as a small, dependency-free static dashboard for contributors. It should present multiple projects in readable cards, make ownership, status, recent activity, and priority easy to scan, and run from VS Code’s **Run Project Pulse Dashboard** configuration.

Repository context: the brief is in `.github/project-pulse-brief.md`; the app directory currently has no implementation files. There is no existing frontend framework, package manifest, or app test suite to extend. Keep the implementation to HTML, CSS, JSON, and the assigned VS Code launch configuration.

## Responsibilities and file ownership

| Owner | Assignment | Responsibilities |
|---|---|---|
| **Planner** | `docs/project-pulse-plan.md` | Record implementation scope, assignments, dependencies, assumptions, and validation. This plan is the handoff to the Orchestrator. |
| **Orchestrator** | Coordination; no implementation file ownership | Confirm the shared UI/data contract, delegate the scoped tasks below, resolve blockers, and verify the integrated result. |
| **Designer** | `app/styles.css` | Define visual hierarchy, spacing, typography, accessible color and status/priority treatments, and responsive behavior. Include `.dashboard` and `.project-card`; use clear badges, rounded corners, and shadows. Provide markup-hook guidance to Coder, but do not edit `app/index.html`. |
| **Coder** | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` | Implement semantic page structure, data loading and rendering, representative project data, and the runnable preview configuration. Follow the agreed markup hooks and data contract; do not edit `app/styles.css`. |

File ownership is exclusive: Designer owns the stylesheet; Coder owns the HTML, JSON, and launch configuration. Any review feedback should be communicated to the owner rather than applied to another agent’s files.

## Ordered implementation steps

### 1. Confirm the implementation contract

**Owner:** Orchestrator, with Designer and Coder input  
**Files:** No implementation files

Before parallel work begins, agree on:

- A top-level `projects` array. Every project has `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Stable HTML/CSS hooks, including `.dashboard` and `.project-card`, plus any badge or grid hooks the Designer and Coder agree on.
- The visible fields and their labels, so the HTML, data, and styling describe the same interface.
- The preview command, port, working directory, and browser URL described in step 4.

**Dependency:** This contract unblocks parallel implementation and avoids mismatched selectors or data fields.

### 2. Build the presentation, data, and preview configuration

Once the contract is agreed, these tasks can run **in parallel** because they have distinct file ownership:

- **Designer — `app/styles.css`:** Create polished, responsive card and dashboard styles. Ensure status and priority remain understandable without color alone, and that text remains readable at narrow viewport widths.
- **Coder — `app/project-data.json`:** Add a valid top-level `projects` array with multiple representative entries and all required fields.
- **Coder — `.vscode/launch.json`:** Add the named launch configuration, with its working directory set to `${workspaceFolder}/app`, using `python3 -m http.server 5500` and opening `http://localhost:%s/index.html` through `serverReadyAction`. Use strict JSON with no comments.

**Dependencies:** All three tasks depend on step 1. The launch configuration does not depend on the completed HTML, but final runtime validation does.

### 3. Implement the page and connect it to the data

**Owner:** Coder  
**File:** `app/index.html`

Create the “Project Pulse” page, reference `styles.css`, load `project-data.json`, and render one `.project-card` per project. Show each required value, including `status`, `recentActivity`, and `priority`. Use semantic structure and accessible labels. Handle data as text rather than inserting project values as HTML.

**Dependencies:** Depends on the step 1 contract and the schema agreed in step 2. It can overlap with step 2 only if Coder and Designer have agreed on the data and class hooks; otherwise, build it after those decisions are settled.

### 4. Integrate and validate

**Owners:** Coder for fixes in Coder-owned files; Designer for fixes in `app/styles.css`; Orchestrator coordinates  
**Files:** All four implementation files

Validate structure and syntax first, then run the launch configuration and inspect the rendered dashboard. Route any fixes to the file’s assigned owner. Do not begin final acceptance until all four files are integrated.

## Dependencies and sequencing

- **Must be sequential:** Agree on the UI/data contract before implementation; complete integration before final runtime validation.
- **Can run in parallel:** After the contract is fixed, Designer’s `app/styles.css` work and Coder’s `app/project-data.json` and `.vscode/launch.json` work. The HTML can also be built during this phase if Coder and Designer have agreed on the data and class hooks; otherwise, build it after those decisions are settled.
- **Integration dependency:** `app/index.html` must reference the actual stylesheet and JSON paths, use selectors present in the stylesheet, and render the agreed schema. The launch configuration must serve the app directory and open the page rather than the directory root.

## Assumptions and open questions

- **Assumption — sample project content:** The repository contains no authoritative project names, owners, activity, statuses, or priorities. Use clearly illustrative sample entries; do not present invented details as verified team facts.
- **Assumption — contributor-friendly summary:** The brief requests a short contributor-friendly summary but does not define a `summary` field in its required schema. For this implementation, treat concise `recentActivity` text as the summary rather than adding an unrequested field. Confirm whether a separate summary field is desired before expanding the schema.
- **Open question — priority/status vocabulary:** No approved values or mapping are specified. Keep sample values simple and display them as readable text; do not imply a fixed operational taxonomy.
- **Open question — launch debugger availability:** The launch configuration needs a VS Code Python/debugger type capable of starting the specified server command. Confirm the required debugger support is available in the Codespace. The repository does not declare an extension for this configuration.
- **Open question — browser behavior:** The exercise expects the launch action to open the dashboard externally. Confirm that this is the intended behavior in the target Codespace/VS Code environment.

## Edge cases and risks

- **Data loading:** Loading JSON with `fetch` requires serving the page over HTTP; opening `index.html` directly with `file://` may fail. Validate via the launch configuration.
- **Missing or malformed data:** Provide a visible empty or error state if the project list is absent, empty, or cannot be loaded; avoid a blank dashboard or uncaught failure.
- **Port conflict or server startup failure:** Port `5500` may already be occupied, or Python may not be available. If the server does not start, the readiness action cannot open the page; report the failure clearly during validation.
- **Readiness URL matching:** `serverReadyAction` depends on matching server output. Confirm the readiness pattern works with the configured Python server and opens `/index.html`.
- **Accessibility and responsive layout:** Status/priority must not rely on color alone. Check contrast, heading structure, readable spacing, and narrow viewport behavior.
- **Untrusted text rendering:** Even for local sample data, render field values as text rather than HTML to avoid interpreting unexpected markup.

## Validation expectations

1. **File and content checks**
   - Confirm `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` exist.
   - Confirm the page title is exactly **Project Pulse**, and the HTML references both `styles.css` and `project-data.json`.
   - Confirm the HTML renders multiple `.project-card` elements from the data and displays project name, owner, status, recent activity, and priority.
   - Confirm the stylesheet includes `.dashboard` and `.project-card`, with responsive layout, readable spacing, rounded corners, and shadows.
   - Confirm `app/project-data.json` parses as JSON, has a top-level `projects` array, and each entry has the five required fields.
   - Confirm `.vscode/launch.json` parses as strict JSON and contains **Run Project Pulse Dashboard**, the `app/` working directory, the specified server command/port, and a URL ending in `/index.html`.

2. **Runtime check**
   - Start **Run Project Pulse Dashboard** in VS Code.
   - Confirm the browser opens the Project Pulse UI at `index.html`, not a directory listing.
   - Confirm multiple cards and all required project fields are visible.
   - Confirm the layout remains usable at a narrow viewport.
   - Confirm a data-loading failure is handled visibly, if practical to trigger during manual testing.
   - Stop the preview server after testing.

3. **Repository check**
   - Run `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`.
   - Review the Step 3 checks in `.github/workflows/3-step.yml` and the exercise validation script in `scripts/validate-exercise.sh` for the repository’s expected file names and required strings. These checks cover file/content and JSON requirements; they do not replace the runtime check.

## Documentation consulted

- Repository brief: `.github/project-pulse-brief.md`
- Agent ownership guidance: `.github/agents/designer.agent.md`, `.github/agents/coder.agent.md`, `.github/agents/orchestrator.agent.md`
- Exercise requirements and checks: `.github/steps/3-step.md`, `.github/workflows/3-step.yml`, `scripts/validate-exercise.sh`
- VS Code launch configuration and `serverReadyAction`: [VS Code debugging configuration](https://code.visualstudio.com/docs/debugtest/debugging-configuration)
- Python static server behavior and limitations: [Python `http.server`](https://docs.python.org/3/library/http.server.html)
