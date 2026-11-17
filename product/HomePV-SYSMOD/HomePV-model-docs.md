# HomePV model: documentation to add in Cameo

Paste each text into the element's Documentation field. Grouped by package, in model order.

## HomePVSystemModel

### (the package itself)

```text
HomePV: a grid-tied residential photovoltaic system with battery storage,
modeled in SysML v2 with the SYSMOD method. Use it as a worked example of
the whole SYSMOD chain, from the problem statement to the physical
architecture.

HOW TO READ IT. Every SYSMOD step is its own package inside the project
container HomePVSystemProject. Each package imports only the earlier
packages it needs:
  HPVS-ProblemSpace            Step 1 problem statement, Step 2 system idea
                               (base architecture), Step 3b stakeholder needs
  HPVS-Stakeholders            Step 3 stakeholders and elicitation priority
  HPVS-Requirements            Step 4 system requirements
  HPVS-ItemsDefinitions,
  HPVS-PortDefinitions,
  HPVS-Context                 Step 5 what crosses the boundary, where, and
                               with whom (system context and actors)
  HPVS-Functions, HPVS-UseCases  Step 6 use cases and their action flows
  HPVS-FunctionalBreakdown     Step 7a hierarchy of functions
  HPVS-FunctionalArchitecture  Step 7b functional parts and interfaces
  HPVS-LogicalArchitecture     Step 8 logical parts (with a small simulation)
  HPVS-PhysicalArchitecture    Step 9 physical components

TRACEABILITY. Needs frame the problem statement, requirements frame the
needs, use cases refine the requirements, their actions use the
functions, functions are allocated to functional parts, functional parts
to logical parts, logical parts to physical components, and the physical
system satisfies the requirements.

ONE SYSTEM, FOUR DESCRIPTIONS. HomePVSystem-BA (first structural outline),
-FA (functional), -LA (logical) and -PA (physical) describe the same
system of interest. They are four views of one system, not four systems.
```

## 'HPVS-ProblemSpace'

### (the package itself)

```text
Steps 1, 2 and 3b (Problem Statement, System Idea, Stakeholder Needs):
why the system exists and what it is meant to be.
Design decisions:
- The problem statement is a concern, not a requirement: needs and
  requirements "frame" it, and frame only resolves against a concern.
- The system idea is the base architecture HomePVSystem-BA itself; there
  is no separate black-box system part. Its elevator pitch is the doc of
  the systemOfInterest in HPVS-Context.
- Each need is a concern in the stakeholder's own voice. All needs share
  one subject, theHousehold (the problem domain), and sit at the same
  level as problemStatement so that frame resolves.
```

### part def <'HPVS-BA'> 'HomePVSystem-BA'

```text
Base architecture (Step 2, System Idea): the first structural outline of
the system as six peer subsystems. It is deliberately flat and coarse;
the functional, logical and physical architectures refine it. The system
of interest in the context is typed by it.
```

## 'HPVS-Stakeholders'

### (the package itself)

```text
Step 3 (Stakeholder Analysis). priority = risk * effort is the SYSMOD
elicitation priority: risk is the harm if a stakeholder is ignored,
effort the work needed to elicit their needs. It is not raw importance.
Levels start at low = 1; there is no "none" level, because a stakeholder
with no risk and no effort would not be modeled at all.
```

## 'HPVS-Requirements'

### (the package itself)

```text
Step 4 (Requirements). Every requirement frames at least one stakeholder
need, which gives the trace need -> requirement. The subject of every
requirement is the system of interest, so only the system as a whole can
satisfy them (see the satisfy statements in HPVS-PhysicalArchitecture);
a single component cannot.
```

## 'HPVS-ItemsDefinitions'

### (the package itself)

```text
Step 5: the things that cross the system boundary or move between parts.
Each item names a physically real quantity or a concrete piece of
information, never a field such as "electricity". General kinds are
specialized where a context needs it (DC/AC energy, grid exchange status).
```

### item def SolarIrradiation

```text
Solar radiation arriving on the roof surface: the system's primary energy input.
```

### item def ElectricalEnergy

```text
Electrical energy in general. Specialized into DC and AC, because where the system converts between them is a design decision the model must show.
```

### item def DCElectricalEnergy :> ElectricalEnergy

```text
DC energy as the PV modules produce it and the battery stores it. The energy decision is taken on the DC side.
```

### item def ACElectricalEnergy :> ElectricalEnergy

```text
Grid-compatible AC energy, as the household uses it and the public grid exchanges it.
```

### item def MonitoringData

```text
Current performance values: generation, consumption, storage level, feed-in and avoided CO2 (requirement provideMonitoring).
```

### item def MeasurementHistory

```text
Monitoring data recorded over time, so past performance can be shown.
```

### item def ViewRequest

```text
The homeowner's request to see performance. It triggers the visualization.
```

### item def GridConnectionCommand

```text
The decision to connect to or isolate from the public grid (anti-islanding, requirement disconnectOnGridOutage).
```

### item def ConfigurationData

```text
Settings the installer enters at commissioning. No function consumes it yet (use case 'commission the system' has no action flow).
```

### item def CommissioningReport

```text
Report confirming correct operation after commissioning. No function produces it yet (use case 'commission the system' has no action flow).
```

### item def ExchangeStatus

```text
Reading of an energy exchange: which direction the energy goes and how much. General on purpose; contexts narrow it.
```

### item def GridExchangeStatus :> ExchangeStatus

```text
Exchange reading at the grid connection point: importing or exporting, and how much.
```

## 'HPVS-LogicalArchitecture'

### (the package itself)

```text
Step 8 (Logical Architecture). Logical parts commit to a technical
principle (Inverter, EnergyMeter) but never to a product. In this model
each functional part is allocated to exactly one logical part. The names
differ on purpose so that the two layers stay distinguishable.
The package also holds a small executable simulation, used to
demonstrate the behaviour:
- SolarEnvironment varies the irradiance.
- photovoltaicGenerator turns it into produced energy.
- energyMeter accumulates the household consumption.
- energyManager compares the two and sends Connect or Disconnect.
- gridInterconnector follows those commands.
The simulation values are dimensionless demonstrator numbers, not
datasheet values.
```

### item def Connect

```text
Command signal of the simulation: the EnergyManager tells the GridInterconnector to connect to the grid.
```

### item def Disconnect

```text
Command signal of the simulation: the EnergyManager tells the GridInterconnector to disconnect from the grid.
```

### part def SolarEnvironment

```text
Simulation stand-in for the sun, not part of the system. The irradiance
rises in steps of 0.2 per second up to maxLevel, then falls back to
minLevel, and so on, to emulate a varying solar input.
```

### part def <'HPVS-LA'> 'HomePVSystem-LA' > part photovoltaicGenerator : PhotovoltaicGenerator

```text
Simulation: pvPower = irradiance * panelArea * efficiency, added to
dc.energyProduced every second.
```

### part def <'HPVS-LA'> 'HomePVSystem-LA' > part energyManager : EnergyManager

```text
Simulation of the energy decision: while accumulated consumption exceeds
accumulated production it commands Connect, otherwise Disconnect.
```

### part def <'HPVS-LA'> 'HomePVSystem-LA' > part energyMeter : EnergyMeter

```text
Simulation: adds totalLoadPower to status.energyConsumed every second.
```

### part def <'HPVS-LA'> 'HomePVSystem-LA' > part gridInterconnector : GridInterconnector

```text
Simulation: switches between connected and disconnected on the manager's Connect and Disconnect commands.
```

### interface def PVGeneratorToEnergyManager

```text
Carries the produced energy from the PV generator to the energy manager for the simulation.
```

### interface def EnergyMeterToEnergyManager

```text
Carries the measured consumption from the energy meter to the energy manager for the simulation.
```

## 'HPVS-PortDefinitions'

### (the package itself)

```text
Step 5: one port definition per system boundary. The system of interest
uses the plain port, the actor the conjugated one (~). The grid port is
inout because one physical connection carries energy both ways; the
household port is out only because the household only receives.
```

### port def SunPort

```text
Where solar irradiation enters the system.
```

### port def GridPort

```text
The grid connection point: one AC connection, energy flows in both directions (surplus out, shortfall in).
```

### port def HouseholdPort

```text
Where the household is supplied with AC energy.
```

### port def HomeownerPort

```text
The homeowner's interaction: a view request goes in, monitoring data comes out.
```

### port def InstallerPort

```text
The installer's interaction at commissioning and service. Open finding:
no function consumes ConfigurationData or produces CommissioningReport
yet, so this port does not reach the functional architecture.
```

## 'HPVS-Context'

### (the package itself)

```text
Step 5 (System Context & Actors). Design decisions:
- An actor that is also a stakeholder subsets that stakeholder by
  qualified name (household, homeowner, installer), which traces
  stakeholder -> actor. The interaction port sits on the actor, not on
  the stakeholder.
- The sun and the physical public grid are actors but not stakeholders,
  so they stand alone. The grid operator and the regulatory authority
  are stakeholders without a runtime interaction, so they have no actor.
- The interfaces live at the context level, not inside the system.
```

### part systemContext > part household

```text
The household members as actor: they receive the AC supply.
```

### part systemContext > part homeowner

```text
The homeowner as actor: requests and receives performance information.
```

### part systemContext > part installer

```text
The installer as actor: configures, commissions and services the system.
```

## 'HPVS-Functions'

### (the package itself)

```text
Function definitions used by the use cases (Step 6) and the functional
breakdown (Step 7a). Design decisions:
- Functions are solution-neutral and have parameters, not ports. Ports
  only appear on parts in Step 7b.
- A definition says what a function is, a usage says where it happens:
  ReadExchange is general, and its usage readGridExchange narrows the
  item types by redefinition.
- The energy decision stays on the DC side. ConvertToAC is the single
  DC -> AC boundary.
- Controllers (CoordinateEnergyDistribution, MonitorSystemPerformance)
  are black boxes here.
- SupplyElectricalEnergy and CapturePerformanceData are the composite
  nodes of the Step 7a breakdown.
```

## 'HPVS-UseCases'

### (the package itself)

```text
Step 6 (Use Cases). Design decisions:
- Use cases refine requirements (#refinement); they do not satisfy them.
  Satisfaction is stated in Step 9.
- The action flows use flows only, without successions, because supply
  and monitoring run continuously rather than as sequences.
- Every function parameter is connected, and the actor ends match the
  Step 5 context ports in item type and direction.
Open finding: 'commission the system' and 'maintain and service the
system' have no action flow yet. That is why the requirements they refine
(electrical safety, permitting, commissioning, servicing) are not
satisfied by the physical architecture.
```

## 'HPVS-FunctionalBreakdown'

### (the package itself)

```text
Step 7a (Functional Breakdown): a forest of three root functions, with no
umbrella node. supplyElectricalEnergy and capturePerformanceData run
continuously; visualizeSystemPerformance runs on the homeowner's request,
independently of both, so it is a root of its own. readGridExchange is
owned by the supply function and only referenced (ref action) by the
capture function: one definition, one owner, any number of references.
```

## 'HPVS-FunctionalArchitecture'

### (the package itself)

```text
Step 7b (Functional Architecture). Functional parts are solution-neutral
blocks of responsibility, named as doers. Assigning functions to parts is
the only decision in this step; everything else is derived from it:
- A use case flow between two functions in the same part stays internal.
- A flow crossing two parts gives each part a port and creates one
  interface per part pair.
- Port definitions are named after what they carry (DCPort, ACPort), not
  after who uses them.
- Boundary ports reuse the Step 5 port definitions, so a mismatch with
  the context is a validator error, not a comment.
- The grid sensor is its own part: observing is not acting.
The InstallerPort is missing from HomePVSystem-FA on purpose. No function
serves it yet (see HPVS-UseCases).
```

### port def DCPort

```text
One-way DC energy, from producer to consumer. energyProduced carries the produced energy for the Step 8 simulation.
```

### port def DCExchangePort

```text
DC energy in both directions over one connection: charging and discharging the battery.
```

### port def ACPort

```text
One-way AC energy, from the converter to the connection point.
```

### port def ACExchangePort

```text
AC energy in both directions over one connection: the grid side of the connection point.
```

### port def GridStatusPort

```text
The grid exchange reading, fanned out to everyone who needs it. energyConsumed carries the consumption for the Step 8 simulation.
```

### port def GridCommandPort

```text
The connect/isolate command from the deciding part to the switching part.
```

### port def PerformanceDataPort

```text
Current monitoring data and recorded history, from the recorder to the presenter.
```

## 'HPVS-PhysicalArchitecture'

### (the package itself)

```text
Step 9 (Physical Architecture). Components are named as the things they
are, not as doers. The only decision is which logical parts are deployed
together in one device:
- PhotovoltaicArray        <- PhotovoltaicGenerator
- Energyhub (hybrid inverter) <- Inverter, EnergyManager, GridInterconnector
- LFPBatteryTower          <- Accumulator
- SmartMeter               <- EnergyMeter
- MonitoringGateway        <- PerformanceTracker
- MobileApp                <- PerformancePresenter
Consequences: the logical interfaces between the inverter, the energy
manager and the grid interconnector are now inside the Energyhub, so
ACPort and GridCommandPort no longer appear between components. The
battery is DC-coupled to the Energyhub, so stored energy is converted
only once. The smart meter is a device of its own because it is the
billing point the grid operator owns.
```

### part homePVSystem : 'HomePVSystem-PA'

```text
The physical system as installed. It subsets the system of interest of
the context because every requirement has that system as its subject:
only the whole system can satisfy them. Not satisfied, and reported as
open results rather than filled with invented parts:
beSafeAndLowMaintenance, complyWithElectricalSafety, meetPermitting,
supportCommissioning, supportServicing. They trace to the commissioning
and servicing use cases, which have no action flows, and to the absence
of a protection or fault-handling function.
```

### part def <'HPVS-PA'> 'HomePVSystem-PA' > interface connect pvArray.dcOut to energyhub.dcIn

```text
DC string cabling from the roof array to the Energyhub's PV inputs.
```

### part def <'HPVS-PA'> 'HomePVSystem-PA' > interface connect energyhub.dcBuffer to lfpBatteryTower.dcPort

```text
DC battery cable: one connection carries both charging and discharging (DC-coupled storage).
```

### part def <'HPVS-PA'> 'HomePVSystem-PA' > interface connect smartMeter.acPort to energyhub.acGrid

```text
AC power cable between the grid connection point and the Energyhub.
```

### part def <'HPVS-PA'> 'HomePVSystem-PA' > interface connect smartMeter.statusOut to energyhub.statusIn

```text
Meter data line (typically RS485/Modbus): the energy manager inside the Energyhub reads the grid exchange.
```

### part def <'HPVS-PA'> 'HomePVSystem-PA' > interface connect smartMeter.statusOut to monitoringGateway.statusIn

```text
The same meter reading, fanned out to the monitoring gateway for recording.
```

### part def <'HPVS-PA'> 'HomePVSystem-PA' > interface connect monitoringGateway.dataOut to mobileApp.dataIn

```text
Home network / internet link from the gateway to the homeowner's app.
```
