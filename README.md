# STM-Needle-Fab-Controller
College of Marin Physics Club Needle Fabrication Unit Controller Project

## What it is

STM tips are commonly made by electrochemically etching tungsten wire in sodium hydroxide. The wire necks down at the liquid surface until the lower section drops off, and the etching current falls abruptly. Etching that continues after the break blunts the tip, so the faster the current is cut, the sharper the tip. This project is an open, low-cost controller that detects the break and cuts the current in hardware, with a microcontroller for arming, display, alarm and logging.

## How it works

The cell current flows through a low-side switch and a small sense resistor to ground. The voltage across the sense resistor goes through an op-amp differentiator, which responds to the fast current drop at the break and largely ignores the slow decline before it. A fast latching comparator compares the differentiator output with an adjustable threshold and turns the switch off, and the latch holds that decision until it is reset. The cut-off path involves no firmware. A Teensy 4.1 sets the threshold, arms and resets the latch, drives the displays and buzzer, and can log the etching current.

## Operator interface

The front panel uses industrial-style controls on a small panel. Three five-digit seven-segment displays, driven over SPI by the Teensy, show the run time, the applied etch voltage, and the etching current. A Power toggle and a Solution Voltage toggle each have an indicator LED. Knobs adjust the etch voltage and the lowering and raising speeds of the wire stage, and pushbuttons cover Start, Abort, and manual raise and lower. They are read by the Teensy through pull-up inputs, and driven by the Teensy through MOSFETS

## Design choices

- **Low-side current sensing**, so the signal doesn't depend on the supply voltage or pick up its ripple.
- **A differentiator instead of a fixed current level**, because the current at drop-off varies with wire diameter and immersion depth.
- **A latch**, because the detector output is only a brief pulse.
- **Safe signal polarity**: switching the current on moves the detector away from its trip point, so a restart doesn't trigger it.
- **Quiet power**: linear regulators for the analog and etch rails, since switching noise looks like events to a high-gain differentiator. A buck converter is used only for the digital loads.

## Methods

**Simulation (KiCad).** A behavioral cell model with a slow current decline followed by a fast drop, plus noise sources, is used to sweep the edge speed and threshold. The goal is a detector that trips on the drop-off but not on noise or the decline.

**Measuring the real cell.** Before building the board, we plan to wire a bench supply, the cell and a 10 ohm sense resistor, and record the whole etch, the noise before the break, and the break itself with a single-shot oscilloscope trigger over several etches. The size and speed of the break, compared with the largest noise slope, decide whether the detector works and how to size it. The captured waveforms are then replayed in simulation.
