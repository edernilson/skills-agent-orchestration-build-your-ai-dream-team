# Agent team for Mona's Project Pulse dashboard

I am using GitHub Copilot CLI in a Codespace to orchestrate the work and coordinate a small specialist team for the dashboard build.

- Planner — Claude Opus 5.5 (copilot)
  - Responsibility: researches the repo, identifies requirements and edge cases, and produces an implementation plan with file assignments and sequencing.
  - Definition: `.github/agents/planner.agent.md`

- Orchestrator — Claude Opus 5.5 (copilot)
  - Responsibility: breaks the work into phases, delegates tasks to the specialist agents, and verifies the integrated result before reporting back.
  - Definition: `.github/agents/orchestrator.agent.md`

- Designer — Gemini 3.7 Flash (copilot)
  - Responsibility: shapes the dashboard UX and visual design, focusing on usability, accessibility, interaction flow, and polished responsive styling.
  - Definition: `.github/agents/designer.agent.md`

- Coder — GPT-5.5 (copilot)
  - Responsibility: implements the assigned code changes, follows the plan, validates behavior, and writes the feature logic within the scoped files.
  - Definition: `.github/agents/coder.agent.md`

This team is intentionally split so the Orchestrator can coordinate planning, design, and implementation while the Copilot CLI in the Codespace keeps the workflow structured and easy to supervise.
