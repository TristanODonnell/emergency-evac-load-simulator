# Emergency Evacuation Load Simulator — Planning Document

## 1. Project Motivation
- Why evacuation modeling matters
- Why abstraction is acceptable
- What question the simulator answers

## 2. Assumptions
Explicitly state:

- What is simplified 
- What is ignored 
- Why those omissions are acceptable

## 3. System Model
- Node definitions 
- Edge definitions 
- Population semantics 
- Time semantics

## 4. Decision Strategies
- What information it sees 
- What decisions it makes 
- What it cannot override

## 5. Metrics
- Define each metric in plain language 
- Explain why it matters 
- Define success vs failure

## 6. Scenarios
- Scenario descriptions 
- Intended stress conditions 
- Expected qualitative outcomes (not numeric)

## 7. Reproducibility Rules

## 8. Risks & Limitations
- Where the model could mislead 
- What would be needed for realism 
- Where results should not be over-interpreted


## Reproducibility Rules

- All simulations must be deterministic by default
- If randomness is introduced (e.g., tie-breaking), it must be seeded
- Given the same scenario config and strategy config, outputs must be identical
- Each run is identified by a run ID derived from inputs
- Outputs are stored without mutation
