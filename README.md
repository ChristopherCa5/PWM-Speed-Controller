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
  - All other values fall between 1–2 ms(only 5% difference between high and low signals)

## First Circuit: Just a Switch (No PWM)

### Idea

Initially, I just wanted a simple on/off switch so I could switch the Nerf gun on and off. To do this, I wanted to average the servo PWM input, then build a circuit that could take that filtered signal and amplify the difference between low and high, so that a low signal would be sent to ground and a high signal would be sent to 5V. I used an op-amp for this, since it could produce large changes in the output for small changes in the input.

The input voltage was sent through a cascaded RC filter to average the signal while keeping an appropriate response time. I used an LM311 comparator to compare a DC reference voltage to the filtered input voltage.

I simulated this circuit in LTspice.

### First Circuit Simulation
![First LTspice simulation](Pictures%20and%20Videos/First_LTspice_simulation.png)

### Built on Breadboard

![Breadboard build](Pictures%20and%20Videos/20260521_105732.jpg)

### Circuit Built on Protoboard

![Protoboard build](Pictures%20and%20Videos/20260528_143124.jpg)


## Adding PWM

### Idea

Adding triangular waves to the inverting input of the op-amp changes the duration the op-amp output stays on, based on how high the non-inverting input is compared to the triangle wave.

- Example: if the non-inverting input is high compared to the triangle wave, the output will be on for a long duration (higher duty cycle).
- If the non-inverting input is low compared to the triangle wave, the output will only be on for a short amount of time (lower duty cycle).

To create the triangle waves, I generated a clock signal and then integrated it. The ECE lab had a chip with six inverters (CD4069). I used three of the inverters from this chip to create a clock signal, then passed that clock signal through an RC filter to average (integrate) it, producing triangle waves. I also added a pulldown resistor to pull the triangle waves down to the voltage the averaged input would be at (RC filter plus a voltage divider).

### Simulation

![Triangle wave generation simulation](Pictures%20and%20Videos/20260526_232609.jpg)

I later added a filter at the output just for nicer viewing in LTspice (this in not included in the actual built circuit).

Since I didn't have much time, I only built a breadboard version to test the triangle waves.

### Breadboard — Generating Clock and Triangle Waves

![Breadboard clock and triangle wave generation](Pictures%20and%20Videos/20260527_141240.jpg)

### Built on Protoboard and connected to original circuit

![Triangle wave protoboard build](Pictures%20and%20Videos/20260528_152828.jpg)

I built an extra board to generate the triangle waves so I could feed them into the inverting input instead of the voltage divider.

This board ended up not working since something was shorting, but I wasn't able to determine the cause since everything was soldered very compactly. I also didn't want to continue using this circuit just incase the short ended up breaking something, which would waste a lot of time for me to just find out a part is broken.

### Full Circuit on Breadboard

To make sure everything would work as in simulation, I built a complete breadboard version of the circuit(probably should have done this in the beginning).

![Complete breadboard circuit](Pictures%20and%20Videos/20260528_232023.jpg)

### Second Protoboard with Probe Points

I built another protoboard and added probe points to verify everything was working correctly, tested against the breadboard circuit.

![Protoboard with probe points](Pictures%20and%20Videos/20260530_183436.jpg)

This circuit ended up having two broken connections:
1. Ground was disconnected from the RC input filter.
2. Output was not connected to the pullup resistor.

These would have been very difficult to find without the probe points, and the breadboard circuit to input signals that I knew worked.

## Completed LTspice Circuit

![Final speed controller schematic](Pictures%20and%20Videos/Final_Speed_controller.png)

I didn't have time to implement the charge pump gate driver, but the circuit still worked well enough without it. I was originally going to make two of these circuits, but I had finals, so I ran out of time. I would have used one with the gate driver for the flywheels of the nerf gun, and one without the charge pump for the feeder. I wanted the gate driver since the nmos transistors I was using were still in the edge of their saturation region, meaning they still weren't completely switched on. Since we weren't driving very big motors, this ended up not really being a problem.

### LTspice Simulation Results

![LTspice simulation results](Pictures%20and%20Videos/LTspice_sim_results.png)

Using 5 different input signals from 1ms to 2ms, the output PWM duty cycle increases from 0 to 100%.

### PWM Output Measured from the Oscilloscope

https://github.com/user-attachments/assets/ff06a7a5-2d89-42f0-9809-bfd7686500d2

Output PWM duty cycle increases as the input increases.

## Finished Circuit

![Finished circuit](Pictures%20and%20Videos/IMG_8663.JPG)

## Demo Video on RC car
[![Watch the Demo](https://img.youtube.com/vi/I8uCNUa1c4s/maxresdefault.jpg)](https://www.youtube.com/watch?v=I8uCNUa1c4s&t=40s)
