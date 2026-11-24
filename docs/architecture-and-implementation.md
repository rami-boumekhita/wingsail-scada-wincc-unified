# Architecture and implementation notes

## Project objective

Demonstrate Siemens PLC and SCADA engineering using a practical three-wing positioning simulation, with particular emphasis on reusable WinCC Unified HMI components rather than complex process algorithms.

## Step-by-step development summary

1. **Create the TIA Portal V21 project** `WingSail_SCADA` and configure a SIMATIC CPU 1511-1 PN PLC.
2. **Build `UDT_Wing`** with the setpoint, actual angle, operating mode, movement and fault flags.
3. **Build `DB_Wings`** with three `UDT_Wing` instances, giving every wing the same data layout.
4. **Implement `FC_SimulateWing` in SCL.** Simulate incremental movement toward the requested position, fault injection and fault reset.
5. **Call the simulation in `OB_WingSimulation`**, configured for a 100 ms cyclic interrupt (100000 microseconds).
6. **Add `Ready`**, true when the wing is enabled, in AUTO mode and has no active fault.
7. **Configure WinCC Unified PC Runtime**, PLC network and the symbolic HMI connection.
8. **Create structured HMI tags** by mapping the `DB_Wings` members; connect them to live HMI fields.
9. **Design the Wing Overview** with independent angle readouts and three colored wing panels.
10. **Build `FP_WingControl`** with the operating mode, Ready, Moving and Fault indications, setpoint input, and Enable/Disable/Reset/Switch Mode controls.
11. **Create WinCC Unified text lists** for Boolean-dependent messages such as AUTO/MANUAL and OK/FAULT.
12. **Release faceplate library versions**, update all instances in the HMI and map their Tag Interfaces to the corresponding Wing 1/2/3 tags.
13. **Create wing-specific navigation** among overview, control and alarms screens.
14. **Create three discrete alarms**, triggered by the three wing Fault bits.
15. **Style the Alarm Control** to a readable dark theme; simplify the toolbar and columns.
16. **Create `Log_WingAlarms`** and associate the `Alarm` class with it.
17. **Animate three wing rectangles** by dynamically linking rotation angle to each wing's actual angle.
18. **Create `FP_WingFaultStatus`** as a second reusable faceplate for overview fault indicators.
19. **Run and verify** tag displays, wing rotation, faceplate controls, fault alarms and visible history entries.

## Specific technical design choices

- **SCL:** Faster to develop a compact simulation while time was focused on visualisation.
- **UDT + structured DB:** Three structurally identical devices share one data contract.
- **Cyclic OB:** Predictable 100 ms update intervals for simulation increments.
- **Tag Interface:** Separates reusable faceplate design from a particular wing's PLC tag address.
- **Text lists / tag-based color dynamization:** Simple and maintainable for Boolean indications.
- **Alarm class + log:** Keeps handling of active alarm state separate from stored event history.

## Issues solved during development

| Issue | Explanation / resolution |
|---|---|
| PLC security compiling failure | A configured PLC user needed Full access. Adjusted development-only PLC user/role settings; production hardening is outside the demo. |
| Angle did not change during first HMI test | The wing needed an active enable command and a running PLC simulation; verified actual-angle movement. |
| Confusing faceplate assignment options | At the instance level, selected existing HMI tags in the Static value field rather than runtime-generated Tag parameters. |
| Text list / Property Interface confusion | Direct library text-list dynamization worked; Resource list interface properties were optional where not otherwise needed. |
| Faceplate edit not reflected in instances | Released a new library type version and used Update types / Update instances in project. |
| Fault badge faceplate included huge empty space | Resized the faceplate canvas to the content rather than resizing only the rectangle. |
| Wing icon appeared horizontal | Width and height were reversed; corrected to 20 px width and 120 px height before rotation mapping. |
| Alarm Control looked too bright/default | Styled alarm table, column headers, function bar and information bar separately. |
| Alarm columns cut off text | Adjusted useful columns/display and emphasized message readability. |
| Alarm history needed configuration | Added an alarm log and assigned the `Alarm` class; confirmed history entries appeared in Runtime. |

## Verification and limitations

The shared screenshots demonstrate updated angles, ready/auto state and fault history entries. They are not evidence of field commissioning, hardware safety compliance or exhaustive test coverage.

The simulation is intentionally simplified. In a real industrial design, operational safety limits, command authority, PLC interlocks, fail-safe behavior, alarm severity design, authentication and cybersecurity controls would need separate engineering and verification.
