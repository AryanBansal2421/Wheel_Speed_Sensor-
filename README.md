# WSS Sensor Output Validation using LabVIEW

Automated pass/fail test bench for validating Wheel Speed Sensor (WSS) output signals, built during my R&D internship at Continental Automotive India (WSS Department).

## Problem Statement

Active WSS sensors output a square-wave signal whose **frequency** and **peak-to-peak voltage (Vpp)** must stay within spec for the sensor to be considered functional. Manually inspecting waveforms on a scope for every test unit is slow and error-prone. This project automates that check.

![Reference active sensor output](images/reference_sensor_output.png)
*Typical output waveform of a healthy active WSS sensor — oscillates between ~0.78V and ~1.46V, which informed the Vpp threshold used below.*

To validate the logic under realistic conditions, the clean square wave is simulated with added Gaussian noise, replicating the noise a real WSS signal would pick up from wiring/EMI in an automotive environment:

![Simulated noisy input waveform](images/simulated_input_waveform.png)
*Square wave with Gaussian noise added — this is the actual input fed into the frequency and amplitude checks below.*

## How It Works

The VI simulates/acquires a noisy square-wave sensor signal and runs two independent checks in parallel, then combines them into a single verdict:

1. **Frequency check** — Uses the *Tone Measurement* VI to extract the fundamental frequency of the input signal, then verifies it falls within **45–55 Hz** using two comparator blocks.
2. **Amplitude check** — Computes **Vpp** (max − min of the signal) and verifies it falls within **0.8–1.5 V**.
3. **FFT reconstruction** — Splits the frequency-domain signal into real/imaginary components and reconstructs the magnitude (√(re² + im²)) for waveform visualization on a separate graph.
4. **Decision logic** — Both checks (frequency AND amplitude) are ANDed together to drive a single **Pass/Fail** boolean indicator.

![LabVIEW block diagram](images/block_diagram.png)

## Repo Structure

```
├── WSS_Signal_Validation.vi     # Main LabVIEW VI
├── images/
│   ├── block_diagram.png        # Full block diagram
│   ├── reference_sensor_output.png  # Reference active sensor waveform
│   └── simulated_input_waveform.png # Noisy simulated input signal
└── README.md
```

## Tools & Concepts Used

- **LabVIEW** (Signal Processing Toolkit)
- Tone Measurement (frequency-domain analysis)
- FFT-based magnitude computation
- Threshold-based comparator logic
- Boolean AND-gated pass/fail decision system

## Sample Result

Given a signal at ~50 Hz and Vpp ~1.0V, both checks pass and the **Pass/Fail** LED indicates green (True).

## Future Improvements

- Replace simulated signal with live DAQ acquisition (NI-DAQmx) for real sensor hardware
- Auto-log test results (frequency, Vpp, verdict) to CSV per unit under test
- Extend to support passive sensor waveform types (sine-based) alongside active sensors

## Author

**Aryan Bansal** — B.Tech, Instrumentation & Control Engineering, NSUT Delhi
Built during R&D Internship, Wheel Speed Sensor Department, Continental Automotive India
