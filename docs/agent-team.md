# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate a custom team for building Mona's Project Pulse dashboard:

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the specialists, breaks work into phases, assigns explicit file scopes, manages dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository, documentation, dependencies, and edge cases, then produces an implementation plan with file assignments, sequencing, risks, and validation expectations. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements application logic, fixes bugs, creates assigned runnable-app support files, and validates clear, deterministic, testable behavior. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Shapes the dashboard's UI/UX, accessibility, information hierarchy, interaction flow, responsive behavior, and polished visual design. | `.github/agents/designer.agent.md` |

The Orchestrator will have the Planner research and plan the work first, then delegate implementation and design tasks to the Coder and Designer while keeping file ownership clear and integrating their results.
