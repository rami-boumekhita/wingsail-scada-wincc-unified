# PLC logic reference

This document records **verified project structure and snippets** from development. It is **not a substitute for an exported TIA Portal source file**.

## Main data structure — `UDT_Wing`

| Field | SCL type |
|---|---|
| `Angle_Setpoint` | `Real` |
| `Angle_Actual` | `Real` |
| `Auto_Mode` | `Bool` |
| `Enable_Command` | `Bool` |
| `Moving` | `Bool` |
| `Fault` | `Bool` |
| `Simulate_Fault` | `Bool` |
| `Reset_Fault` | `Bool` |
| `Ready` | `Bool` |

## Simulated wing FC

- Function: `FC_SimulateWing`, SCL, using an `InOut` parameter of type `UDT_Wing`.
- Called from cyclic `OB_WingSimulation` every 100 ms for `DB_Wings.Wing_1`, `.Wing_2`, and `.Wing_3`.
- Includes fault-reset, fault simulation and incremental angle-movement logic.

### Verified Ready calculation

```scl
// Wing ready status
#Wing.Ready := #Wing.Enable_Command
               AND #Wing.Auto_Mode
               AND NOT #Wing.Fault;
```

### Verified fault excerpt

```scl
// Fault reset
IF #Wing.Reset_Fault THEN
    #Wing.Fault := FALSE;
    #Wing.Simulate_Fault := FALSE;
    #Wing.Reset_Fault := FALSE;
END_IF;

// Fault simulation
IF #Wing.Simulate_Fault THEN
    #Wing.Fault := TRUE;
END_IF;
```

These snippets were visible during project development and are included for explanation. Export the **complete actual `FC_SimulateWing` and `OB_WingSimulation` SCL** from your own TIA Portal project before claiming that the full PLC code is available in this repository.
