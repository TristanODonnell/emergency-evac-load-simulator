# Emergency Evacuation Load Simulator — Planning Document

## 1. Project Motivation

## 2. Assumptions

## 3. System Model

## 4. Decision Strategies

## 5. Metrics

## 6. Scenarios

## 7. Reproducibility Rules

## 8. Risks & Limitations



## Reproducibility Rules

- All simulations must be deterministic by default
- If randomness is introduced (e.g., tie-breaking), it must be seeded
- Given the same scenario config and strategy config, outputs must be identical
- Each run is identified by a run ID derived from inputs
- Outputs are stored without mutation
