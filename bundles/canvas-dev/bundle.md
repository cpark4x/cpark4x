---
bundle:
  name: canvas-dev
  version: 1.0.0
  description: Personal Canvas/Workspaces2 development bundle with outcome-driven patterns

config:
  allowed_write_dirs:
    - ~/.amplifier/dev-memory
    - ~/amplifier.workspaces2
  
  workspace_context:
    project_name: "Canvas (Workspaces 2.0)"
    local_path: "~/amplifier.workspaces2"
    repo_url: "https://github.com/microsoft/workspaces2"
    philosophy: "outcome-driven development"

includes:
  # Amplifier-dev - stay current with Amplifier developments automatically
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main#subdirectory=bundles/amplifier-dev.yaml
  
  # Dev-memory behavior - persistent local memory
  - bundle: git+https://github.com/ramparte/amplifier-collection-dev-memory@main#subdirectory=behaviors/dev-memory.yaml
  
  # Looper - supervised work loop (keeps working until done)
  - bundle: git+https://github.com/ramparte/amplifier-toolkit@main#subdirectory=bundles/looper
---

# Canvas Development Bundle

My personal Amplifier configuration for Canvas/Workspaces2 development.

## What's Included

**From Amplifier-Dev:**
- All standard tools (filesystem, bash, web, search, task delegation)
- Session configuration and hooks
- Access to all foundation agents (zen-architect, modular-builder, explorer, etc.)
- Automatic updates when foundation evolves

**From Dev-Memory:**
- Persistent memory at `~/.amplifier/dev-memory/`
- Natural language: "remember this:", "what do you remember about X?"
- Work tracking: "what was I working on?"
- Token-efficient architecture (reads delegated to sub-agent)

**From Looper:**
- Supervised work loop tool
- Keeps working until supervisor confirms completion
- User input injection via `./looper-input.txt`

**Canvas-Specific:**
- Pre-configured workspace paths
- Canvas philosophy awareness
- Epic-driven development patterns

## Canvas Context

When working in this bundle, remember:

### Core Philosophy
1. **Outcome-driven:** Start with "What does success look like?"
2. **Verification-first:** Make → Verify → Ship (no speculative features)
3. **Simplicity:** Output ≠ Outcome. Build what's needed, not what's possible.

### Project Structure
- **Frontend:** React + TypeScript (Vite)
- **Backend:** Python (FastAPI) + PostgreSQL
- **Docs:** Epic-driven (docs/02-requirements/epics/)
- **AI Guidance:** CLAUDE.md, ITERATION_PHILOSOPHY.md

### Common Commands
```bash
# Start development environment
cd ~/amplifier.workspaces2
./start-dev.sh

# Run tests
cd backend && pytest
cd frontend && npm test

# Check status
make status
```

## Usage Patterns

### Feature Development
```
"Using looper, implement Epic 04 story: presentation download feature"
"Following canvas-philosophy, build the outcome definition UI"
```

### Code Review
```
"Review this implementation against Canvas philosophy and ITERATION_PHILOSOPHY.md"
```

### Epic Planning
```
"Break down Epic 12 into implementable user stories following the Canvas template"
```

## Memory System

The bundle maintains context about:
- Recent Canvas work and decisions
- Epic status and priorities
- Common patterns and preferences
- Technical debt and follow-ups

Ask: "What have I been working on in Canvas?" or "Remember this Canvas decision: [...]"

---

@foundation:context/shared/common-system-base.md
