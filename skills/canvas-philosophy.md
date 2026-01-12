# Canvas Philosophy

Environmental prompting for Canvas/Workspaces2 development. Creates a cognitive framework where outcomes drive decisions, verification gates progress, and simplicity wins.

**Source:** Canvas project principles, ITERATION_PHILOSOPHY.md, CLAUDE.md

---

## When to Apply

- Feature development for Canvas
- Epic and user story creation
- Code review for Canvas PRs
- Architecture decisions
- Any work on Workspaces2 codebase

---

## The Canvas Mindset

You are working on Canvas - a platform that recouples AI speed with human intention.

The core problem we're solving: **AI has decoupled speed from intention.** You can build 10 things fast when you needed 1 good thing. Output ≠ Outcome.

### Notice the Output Bias

- The urge to build features before defining outcomes
- The pattern-match to "similar projects" without questioning if they solved the right problem
- The assumption that more features = more value
- The satisfaction of "look what we built" before "look what users can achieve"

### Before You Build

- What does success look like for the user?
- How will we know this solves their problem?
- What's the simplest solution for this outcome?
- Can we verify this works incrementally?
- Are we building for the outcome or building for "completeness"?

### Do Not

- Build features without defining outcomes first
- Skip verification steps to "move faster"
- Add speculative features "for future use"
- Copy patterns without understanding why they exist
- Merge code before verifying it achieves the outcome
- Over-engineer solutions

### The Discipline

**Outcome Definition First:** Every feature starts with "What does success look like?" Not "what should we build" but "what should users be able to achieve?"

**Verification-Driven Development:** Make → Verify → Ship. No skipping verification. No "we'll test it later." Every step proves the previous step worked.

**Ruthless Simplicity:** The simplest solution that achieves the outcome. Not the most clever, not the most scalable, not the most "enterprise-ready." The simplest.

**The question is not "What can we build?" but "What outcome are we enabling, and what's the simplest path there?"**

Build what users need to succeed.

---

## Canvas Development Cycle

### 1. OUTCOME DEFINITION
```
User Story: As a [role], I want to [action], so that [outcome]

Outcome Definition:
- Success looks like: [specific, measurable]
- User can: [concrete capability]
- System ensures: [guarantees]
```

### 2. DESIGN
```
Architecture that:
- Achieves the outcome (not more)
- Follows existing Canvas patterns
- Can be verified incrementally
- Stays simple
```

### 3. IMPLEMENT
```
Code that:
- Matches the design
- Includes verification steps
- Has no speculative features
- Follows Canvas patterns
```

### 4. VERIFY
```
Verification that:
- Proves outcome is achieved
- Tests edge cases
- Checks philosophy compliance
- Includes manual testing
```

### 5. SHIP
```
Only when:
- Outcome is verified
- Tests pass
- Philosophy review passes
- Manual testing confirms
```

---

## Canvas Codebase Context

### Tech Stack
- **Frontend:** React + TypeScript, Vite, Tailwind CSS, shadcn/ui, Zustand
- **Backend:** Python (FastAPI), PostgreSQL, SQLAlchemy, Pydantic
- **AI:** Anthropic Claude (streaming responses)
- **Deployment:** Azure Static Web Apps

### Key Patterns

**Frontend:**
```typescript
// Component pattern
export function MyFeature() {
  const store = useMyStore()
  
  if (loading) return <LoadingState />
  if (error) return <ErrorState error={error} />
  
  return <ActualContent />
}

// Store pattern (Zustand)
interface MyStore {
  data: Data | null
  loading: boolean
  error: Error | null
  fetchData: () => Promise<void>
}
```

**Backend:**
```python
# Endpoint pattern
@router.get("/my-endpoint")
async def my_endpoint(
    param: str,
    current_user: User = Depends(get_current_user),
    db: Session = Depends(get_db)
) -> MyResponse:
    """Docstring explaining what this achieves (outcome)."""
    # Validate
    # Query/process
    # Return
```

### File Organization
```
amplifier.workspaces2/
├── frontend/
│   ├── src/
│   │   ├── components/   # Reusable UI components
│   │   ├── pages/        # Page-level components
│   │   ├── store/        # Zustand stores
│   │   └── services/     # API integration
├── backend/
│   ├── app/
│   │   ├── api/v1/endpoints/  # FastAPI endpoints
│   │   ├── models/            # SQLAlchemy models
│   │   ├── schemas/           # Pydantic schemas
│   │   └── services/          # Business logic
├── docs/
│   ├── 02-requirements/epics/  # Epic documentation
│   └── 04-guides/              # Development guides
├── CLAUDE.md                   # AI agent guidance
└── ITERATION_PHILOSOPHY.md     # Verification approach
```

---

## Expected Output Shape

### Before Implementation
- Outcome defined explicitly
- User capability stated clearly
- Verification plan outlined
- Design follows Canvas patterns
- Scope bounded ("This handles X, not Y")

### During Implementation
- Incremental verification at each step
- No skipping of error cases
- Following established patterns
- Type-safe code
- Comments explain *why*, not *what*

### After Implementation
- "What this achieves" summary (outcome)
- "How to verify" checklist (manual steps)
- Tests cover happy path and edge cases
- Philosophy review passed
- Epic updated with implementation details

---

## Philosophy Checklist

Use this for code review:

**Outcome-Driven:**
- ✓/✗ Is the outcome clearly defined?
- ✓/✗ Does this solve the user's actual problem?
- ✓/✗ Did we avoid building extra features?

**Verification-First:**
- ✓/✗ Can we verify this works?
- ✓/✗ Are tests meaningful (not just coverage)?
- ✓/✗ Is there a manual verification path?

**Simplicity:**
- ✓/✗ Simplest solution for the outcome?
- ✓/✗ Following existing Canvas patterns?
- ✓/✗ Any unnecessary abstractions?

**Quality:**
- ✓/✗ Type-safe? (TypeScript types, Python type hints)
- ✓/✗ Error handling complete?
- ✓/✗ Accessible UI? (ARIA labels, keyboard nav)
- ✓/✗ Loading and error states?

**Canvas Patterns:**
- ✓/✗ Matches frontend patterns?
- ✓/✗ Matches backend patterns?
- ✓/✗ Integrates with existing features?

---

## Anti-Patterns

### Over-Engineering
- Building "flexible" systems before knowing what flexibility is needed
- Adding layers of abstraction "for future features"
- Creating frameworks when simple functions would work

### Under-Verification
- "It compiles so it works"
- Skipping manual testing
- Tests that don't verify the outcome

### Feature Creep
- "While we're here, let's also add..."
- "This would be cool to have"
- Building for imagined future use cases

### The Balance
Canvas features should be:
- **Outcome-focused:** Solves a specific user problem
- **Verifiable:** Can prove it works
- **Simple:** No unnecessary complexity
- **Patterned:** Follows Canvas conventions
- **Complete:** Handles errors, loading, edge cases

---

## Usage Examples

**Starting feature work:**
```
"Using canvas-philosophy, implement Epic 04 Story 3: presentation download"
"Apply canvas-philosophy to this outcome definition"
```

**Code review:**
```
"Review this PR against canvas-philosophy principles"
"Does this implementation achieve the outcome? Check canvas-philosophy"
```

**Epic planning:**
```
"Using canvas-philosophy, break down Epic 12 into user stories"
```

---

Build what matters. Verify it works. Keep it simple.
