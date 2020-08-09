# Klipper

Here are any designs and configurations pertaining to a Klipper setup on this machine.

***

## printer.cfg

This file is provided as is, *do not copy and use it directly*.
Please understand how Klipper works first.
Specifically, this config assumes TMC 2208 drivers are used.
It is also using the ` s-curve-exp/scurve-smoothing` branch, this will not work on a stock Klipper install!

What is most useful is probably the GCODE macros:

```
[gcode_macro START_PRINT]
default_parameter_BED_TEMP: 60
default_parameter_EXTRUDER_TEMP: 210
gcode:
    # Start bed heating
    M140 S{BED_TEMP}
    M104 S{EXTRUDER_TEMP}
    # Use absolute coordinates
    G90
    # Reset the G-Code Z offset (adjust Z offset if needed)
    SET_GCODE_OFFSET Z=0.0
    # Set and wait for nozzle to reach temperature
    M109 S{EXTRUDER_TEMP}
    # Wait for bed to reach temperature
    M190 S{BED_TEMP}
    # Bed level - Uncomment if you want to run it every print!
    #G29
    # Home
    G28
    # Move the nozzle near the bed
    G1 Z5 X10 Y140 F3000
    # Set extruder position to 0
    G92 E0
    # Move the nozzle near the bed
    G1 Z0.2 E6 F3000
    # Wipe 80mm
    G1 Y60 F800 E8
    # Wipe a little without extruding
    G1 Y5 F1600
    # Set extruder position to 0
    G92 E0
    # Move the nozzle up to clear
    G1 Z5 F3000

[gcode_macro END_PRINT]
gcode:
    # Turn off bed, extruder, and fan
    M140 S0
    M104 S0
    M106 S0
    # Relative Mode
    G91
    # Move nozzle away from print while retracting
    G1 Z10 E-5 F3000
    # Absolute Mode
    G90
    # Move to allow removal
    G1 X200 Y200 F3500
    # Disable steppers
    M84

[gcode_macro G29]
gcode:
    G28
    BED_MESH_CALIBRATE
    SAVE_CONFIG
    G28
```

These may then be implemented as such in Cura:

Start print

```
START_PRINT BED_TEMP={material_bed_temperature} EXTRUDER_TEMP={material_print_temperature}
```

End print

```
END_PRINT
```

