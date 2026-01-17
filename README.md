# Manufacturing Ramp-up Decision Support Agent (LLM-based)
## 1. Problem Statement (왜 이 문제가 어려운가)

In semiconductor packaging manufacturing, ramp-up decisions are often made under
high uncertainty.

Key inputs such as equipment setup plans, tool availability, and qualification
schedules are frequently not fully documented and instead rely on verbal
agreements, assumptions, and prior experience.

As a result:
  - Capacity ramp-up decisions depend heavily on individual judgment
  - Explanations to customers vary by person
  - Trust in IE capacity data is often challenged
  - Risk is taken implicitly rather than explicitly communicated
This project aims to make ramp-up decision reasoning explicit, explainable, and repeatable using an LLM-based decision-support agent.

## 2. Why LLM (and why not prediction)

This system does not use LLMs to predict capacity values.
Ramp-up feasibility is determined by:
  - Development readiness
  - Equipment & tool availability
  - Qualification constraints
These can be evaluated through rules and schedules.
However, explaining why a specific ramp-up week is feasible or not requires:
  - Combining structured data (weeks, lead times)
  - Unstructured context (assumptions, agreements, risk tolerance)
  - Organizational decision bias (schedule commitment vs quality risk)
LLMs are used here as a reasoning and explanation layer, not a prediction model.

## 3. Core Decision Concept

The agent evaluates ramp-up feasibility based on constraint satisfaction, not
numerical optimization.
Key constraints:
  - Development completion
  - Equipment setup readiness
  - Tool availability
  - Qualification duration (fixed, non-compressible unless risk is accepted)
The earliest ramp-up week is the first week where all mandatory constraints are met.

## 4. Assumption-based Decision Modeling

In real manufacturing environments, not all inputs are formally documented.
This agent treats assumptions and verbal agreements as first-class evidence.
  Example: Assumption Log (v1)
    - Product/Module: X / Camera
    - Target Ramp-up Week: W26
    - Development complete by: W25
    - Tool ETA: W17
    - Planned setup week (agreed): W18
    - Qualification duration: 5 weeks
    - Setup owner: NPI Equipment Setup PM (role-based)
All conclusions are explicitly labeled as assumption-based unless formally confirmed.

## 5. Decision Logic (Human → Agent)
Baseline decision
  - If all constraints are satisfied by W26 → Ramp-up feasible at W26

What-if Rule #1: Setup delay (+1 week)
Observed organizational behavior
When ramp-up is customer-committed, the organization typically:
  - Maintains the target week
  - Compresses qualification
-   Accepts higher risk

IF setup_delay <= 1 week
AND no qualification failure
THEN
  - keep ramp-up at W26
  - increase risk level to High
  - 
What-if Rule #2: Qualification issue (+2 weeks)
Observed organizational behavior
When qualification failure occurs:
  - Schedule commitment is deprioritized
  - Ramp-up is shifted to the next feasible window

IF qualification_issue_detected = true
AND additional_qualification_time >= 2 weeks
THEN
  - shift ramp-up week
  - reduce operational risk

## 6. Agent Output Structure (Explainability-first)

Each response follows a fixed structure:
Example Output
Conclusion
Ramp-up at W26 is feasible.

Supporting Reasoning
  - Development completes by W25
  - Equipment setup agreed at W18
  - Qualification completes by W23
  - Ramp-up aligned with post-development readiness
  
Assumptions Used
  - Setup executed as agreed
  - No qualification failure occurs

Risk Assessment
  - Risk Level: Medium / High
  - Risk Drivers: qualification compression, setup slip

Next Required Confirmation
  - Final setup execution plan confirmation by W16

## 7. What This Agent Is (and Is Not)

This agent is:
  - A decision explanation system
  - A domain-aligned reasoning agent
  - A way to externalize manufacturing judgment

This agent is not:
  - A capacity prediction model
  - A scheduling optimizer
  - A replacement for engineering judgment

## 8. Why This Matters

This approach:
  - Makes implicit judgment explicit
  - Improves consistency in customer communication
  - Preserves organizational decision patterns
  - Enables risk-aware ramp-up commitments

It reflects how decisions are actually made, not how they are idealized in
models or documentation.

## 9. Next Steps (Optional Extensions)

  - API-based implementation (FastAPI)
  - Integration with MES / ECIM data
  - Confidence scoring based on assumption maturity
  - Multi-product ramp-up comparison

## Final Note

This project is intentionally focused on decision reasoning and explainability,
as these represent the largest gap between manufacturing reality and traditional
data-driven systems.
