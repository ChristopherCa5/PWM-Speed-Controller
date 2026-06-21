# RC PWM to Flywheel ESC Converter

A custom speed controller that takes a standard RC servo/PWM signal as input and outputs a PWM throttle signal to drive the flywheels of a Nerf gun — built to behave like a normal RC car speed controller.

## Problem Specification

(Constraints I set for myself, since I wanted it to work like a normal RC car speed controller.)

- Must input a standard RC PWM signal and output a signal that can implement PWM throttle control for the flywheel motors of the Nerf gun.
- Must be able to plug different batteries into the speed controller, so the control logic must **not** be powered from motor power. Everything must be powered from the 3-wire servo connection (GND, 5V, signal).
- The RC PWM signal being used as input is **not** a normal PWM signal like the one I wanted to output:
  - 50 Hz signal → refreshes every 20 ms
  - Low signal → 1 ms pulse
  - High signal → 2 ms pulse
  - All other values fall between 1–2 ms

## First Circuit: Just a Switch (No PWM)

### Idea

Initially, I just wanted a simple on/off switch so I could switch the Nerf gun on and off. To do this, I wanted to average the servo PWM input, then build a circuit that could take that filtered signal and amplify the difference between low and high, so that a low signal would be sent to ground and a high signal would be sent to 5V. I used an op-amp for this, since it could produce large changes in the output for small changes in the input.

The input voltage was sent through a cascaded RC filter to average the signal while keeping an appropriate response time. I used an LM311 comparator to compare a DC reference voltage to the filtered input voltage.

I simulated this circuit in LTspice.

### First Circuit Simulation

![PWM ESC LTspice simulation](Pictures%20and%20Videos/PWM%20ESC%20LTspice.png)
![First LTspice simulation](Pictures%20and%20Videos/First_LTspice_simulation.png)

### Built on Breadboard

![Breadboard build](Pictures%20and%20Videos/20260521_105732.jpg)

### Circuit Built on Protoboard

![Protoboard build](Pictures%20and%20Videos/20260528_143124.jpg)

*End of first circuit.*

## Adding PWM

### Idea

Adding triangular waves to the inverting input of the op-amp changes the duration the op-amp output stays on, based on how high the non-inverting input is compared to the triangle wave.

- Example: if the non-inverting input is high compared to the triangle wave, the output will be on for a long duration (higher duty cycle).
- If the non-inverting input is low compared to the triangle wave, the output will only be on for a short amount of time (lower duty cycle).

To create the triangle waves, I generated a clock signal and then integrated it. The ECE lab had a chip with six inverters (CD4069). I used three of the inverters from this chip to create a clock signal, then passed that clock signal through an RC filter to average (integrate) it, producing triangle waves. I also added a pulldown resistor to pull the triangle waves down to the voltage the averaged input would be at (RC filter plus a voltage divider).

### Simulation

![Triangle wave generation simulation](Pictures%20and%20Videos/20260526_232609.jpg)

Since I didn't have much time, I only built a breadboard version to test the triangle waves.

### Breadboard — Generating Clock and Triangle Waves

![Breadboard clock and triangle wave generation](Pictures%20and%20Videos/20260527_141240.jpg)

### Built on Protoboard

![Triangle wave protoboard build](Pictures%20and%20Videos/20260528_152828.jpg)

I built an extra board to generate the triangle waves so I could feed them into the inverting input instead of the voltage divider.

This board ended up not working since something was shorting, but I wasn't able to determine the cause since everything was soldered very compactly.

### Full Circuit on Breadboard

To make sure everything would work as in simulation, I built a complete breadboard version of the circuit.

![Complete breadboard circuit](Pictures%20and%20Videos/20260528_232023.jpg)

### Second Protoboard with Probe Points

I built another protoboard and added probe points to verify everything was working correctly, tested against the breadboard circuit.

![Protoboard with probe points](Pictures%20and%20Videos/20260530_183436.jpg)

This circuit ended up having two broken connections:
1. Ground was disconnected from the RC input filter.
2. Output was not connected to the pullup resistor.

## Completed LTspice Circuit

![Final speed controller schematic](Pictures%20and%20Videos/Final_Speed_controller.png)

I didn't have time to implement the charge pump gate driver, but the circuit still worked well enough without it.

### LTspice Simulation Results

![LTspice simulation results](Pictures%20and%20Videos/LTspice_sim_results.png)

### PWM Output Measured from the Oscilloscope

https://github.com/ChristopherCa5/PWM-Speed-Controller/raw/main/Pictures%20and%20Videos/20260602_144045%20-%20Trim.mp4

> **Note on embedding this video:** GitHub will only auto-render an inline, playable video player in a README if the video was uploaded through GitHub's own web drag-and-drop uploader (in an issue, PR, or the README editor on github.com) — that gives you a `https://github.com/user-attachments/assets/...` link. The repo-relative path above (now pointing at the correct filename, `20260602_144045 - Trim.mp4`, with spaces percent-encoded as `%20`) will **not** autoplay inline; it just renders as a clickable link that opens/downloads the raw file.
>
> To get the real inline player:
> 1. Go to your repo on github.com and open the README in the web editor (or open an Issue/PR — same uploader).
> 2. Drag `20260602_144045 - Trim.mp4` directly into the text box.
> 3. GitHub uploads it and inserts a `https://github.com/user-attachments/assets/...` link automatically.
> 4. Copy that generated link and paste it here in place of the raw-file link above.

## Finished Circuit

![Finished circuit](Pictures%20and%20Videos/IMG_8663.JPG)

## 📽️ Demo Video
[![Watch the Demo](https://img.youtube.com/vi/I8uCNUa1c4s/maxresdefault.jpg)](https://www.youtube.com/watch?v=I8uCNUa1c4s&t=40s)
