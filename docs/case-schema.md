# Case bible schema — v0.2

This is a logical schema for a generated case, not a promise that a prompt-only chatbot can store secrets securely. The facilitator creates one case bible before kickoff and keeps the private portion out of ordinary player messages. In a skills-only plugin, the model still has access to that private portion; motivated players may inspect the distributed materials.

## Required fields

```yaml
case:
  id: string
  schema_version: "0.2"
  settings:
    role: consultant | CDO | steward | project_manager | other
    learning_goal: string
    industry: string
    country_or_region: string | null
    difficulty: beginner | intermediate | advanced
    guidance: simulation | guided | debrief
    regulation_preference: auto | include_if_relevant | exclude | named
    scenario_date: ISO_date
  public:
    organization: string
    business_trigger: string
    stakes: string
    mandate: string
    constraints: [string]
    available_roles: [stakeholder_id]
    initial_evidence: [evidence_id]
    expected_deliverable: string
  private:
    causal_model:
      symptoms: [string]
      causes: [string]
      business_impacts: [string]
      alternative_explanations: [string]
      unknowns_that_may_remain: [string]
    invariants: [string]  # quantities, dates, system behavior, immutable decisions
    stakeholder_profiles:
      - id: string
        role: string
        objectives: [string]
        knows: [fact_id]
        does_not_know: [fact_id]
        may_refer_to: [stakeholder_id | evidence_id]
    evidence:
      - id: string
        title: string
        date: ISO_date
        contents: string
        status: fact | testimony | draft | disputed
        access_path: string
        supports: [fact_id]
        limitations: [string]
    discovery_paths:
      - essential_fact_id: string
        reachable_via: [evidence_id | stakeholder_id]
    regulatory_decision:
      mode: none | fictional_policy | verified_external | hypothetical
      relevance_reason: string
      applicability_assumptions: [string]
      sources:
        - title: string
          issuer: string
          provision: string
          url: string
          checked_on: ISO_date
          source_type: binding_law | guidance | standard | proposal
          scope_note: string
    learning:
      performance_tasks: [string]
      acceptable_approaches: [string]
      common_weak_approaches: [string]
      rubric_dimensions: [string]
      executive_challenges: [string]
  session:  # mutable, separate from the fixed case
    phase: kickoff | discovery | recommendation | review | feedback
    evidence_released: [evidence_id]
    interviews: [stakeholder_id]
    learner_claims: [string]
    decisions: [string]
    open_questions: [string]
```

A generated case may have no external regulatory sources. A verified external rule must have at least one current primary source and a scope note; "fictional_policy" must be plainly labeled as invented for the game. A named regulation the model cannot verify becomes a question to the learner or a hypothetical based on supplied text.

## Preflight checks

1. Every essential fact has a reachable evidence or interview path. A fact deliberately unavailable must be listed under `unknowns_that_may_remain` and cannot be required for full credit.
2. Each artifact has a plausible creator and date, and no item asserts two incompatible immutable values without a deliberate, explained contradiction.
3. Numerical equations and timelines reconcile. If they do not, fix the case before kickoff.
4. Every stakeholder's answer is constrained by `knows` and `does_not_know`; an interview referral has a reachable target.
5. The role has the authority and resources required to attempt the performance tasks. A CDO can decide differently from an outside consultant or steward.
6. At least two defensible interventions exist when the case asks for judgment, and the scoring anchors do not require one branded tool or framework.
7. Regulatory content passes the relevance, jurisdiction, status, and verification gates in [generation-design.md](generation-design.md). A location alone is insufficient.
8. Initial brief includes the business stakes without revealing the causal model.
9. The session state can be summarized to the player without exposing hidden causes.
10. The final evaluator can trace claims to actual learner actions and released evidence.

If a generated case fails a check, repair it once before kickoff. If it cannot be repaired without inventing a current-law claim, remove that legal premise or choose another case. Do not change the private causal model mid-session.