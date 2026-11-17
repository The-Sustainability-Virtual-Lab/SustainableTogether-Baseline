# HomePV-SYSMOD

A SysML v2 model of a **grid-tied residential photovoltaic system with
battery storage**, built with the **SYSMOD** method from the problem
statement down to the physical devices.

**New to modeling? Start with the [walkthrough](WALKTHROUGH.md).** It
explains the model step by step, using the diagrams in `Views/`.

![Overview](Views/HomePVSystemModel__Index.svg)

## What's in this folder

| File / folder | Content |
|---|---|
| [`WALKTHROUGH.md`](WALKTHROUGH.md) | Step-by-step explanation of the model for non-modelers |
| [`HomePVSystemModel.sysml`](HomePVSystemModel.sysml) | The model in SysML v2 textual notation, with `doc` comments explaining each step and its design decisions |
| `HomePV-Baseline.mdszip` | The Cameo (CATIA Magic) project, including the diagrams |
| [`Views/`](Views/) | Diagrams exported from Cameo as SVG |
| [`HomePV-model-docs.md`](HomePV-model-docs.md) | The model documentation as a per-element list, for entering it in Cameo's Documentation fields |

## Model structure

Each SYSMOD step is its own package inside the project `HomePVSystemProject`:

| Package | SYSMOD step | Diagram |
|---|---|---|
| `HPVS-ProblemSpace` | 1 Problem Statement, 2 System Idea (base architecture), 3b Stakeholder Needs | [Problem Statement](Views/HPVS-ProblemSpace__Problem_Statement.svg) |
| `HPVS-Stakeholders` | 3 Stakeholders | — |
| `HPVS-Requirements` | 4 Requirements | [Satisfaction](Views/HPVS-PhysicalArchitecture__physicalArchitectureDiagram.svg) |
| `HPVS-ItemsDefinitions`, `HPVS-PortDefinitions`, `HPVS-Context` | 5 System Context | [System Context](Views/systemContext__System_Context.svg) |
| `HPVS-Functions`, `HPVS-UseCases` | 6 Use Cases with action flows | [Use Cases](Views/HPVS-UseCases__UseCases.svg), [supply](Views/supply_the_household_with_electricity__supply_the_household_with_electricityView.svg), [grid](Views/provide_electricity_to_the_grid__provide_electricity_to_the_gridView.svg), [monitor](Views/monitor_system_performance__monitor_system_performanceView.svg) |
| `HPVS-FunctionalBreakdown` | 7a Functional Breakdown | [Functional Breakdown](Views/HPVS-FunctionalBreakdown__FunctionalBreakdown.svg) |
| `HPVS-FunctionalArchitecture` | 7b Functional Architecture | [Functional Architecture](Views/homePVSystem__homePVSystemView_1.svg) |
| `HPVS-LogicalArchitecture` | 8 Logical Architecture, plus a state-machine simulation | [Logical Architecture](Views/homePVSystem__LogicalArchitecture.svg), [State Machines](Views/homePVSystem__State_Machine.svg) |
| `HPVS-PhysicalArchitecture` | 9 Physical Architecture | [Physical Architecture](Views/HPVS-PhysicalArchitecture__PhysicalArchitecture.svg), [Logical → Physical](Views/HomePVSystemProject__Logical_to_Physical_Mapping.svg) |

Traceability runs the whole way: needs frame the problem statement,
requirements frame the needs, use cases refine the requirements,
functions are allocated to functional parts, those to logical parts,
those to physical components, and the physical system satisfies the
requirements.

## Open points

- Five requirements are not yet satisfied: safe and low-maintenance,
  electrical safety, permitting, commissioning, servicing. The
  commissioning and servicing use cases have no action flows yet, and no
  protection or fault-handling function exists.
- The installer's interaction (configuration in, commissioning report
  out) is defined in the system context but not yet served by any
  function.
- The state-machine simulation uses simplified, dimensionless values.
  `SolarEnvironment` declares `Integer` attributes with fractional
  values, which Syside reports as type errors.
- `HomePVSystem-PA` does not yet replace the base-architecture
  subsystems, so they appear as unconnected parts in
  [this view](Views/homePVSystem__homePVSystemView.svg).

## Tools

- **Cameo / CATIA Magic** (SysML v2): open the `.mdszip` project.
- **Syside** (Sensmetry, 0.8.6): validate the textual model with
  `syside check . -d project`. The `DS_Views` references are
  Cameo-specific view types and are reported as unresolved outside Cameo.
