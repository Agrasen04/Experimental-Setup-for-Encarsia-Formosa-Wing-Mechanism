# Experimental Setup of Encarsia Formosa Wing Mechanism

This repository contains the design, control code, and experimental documentation for a dynamically scaled robotic platform. The setup is designed to replicate the clap-and-fling aerodynamic mechanism utilized by very small insects, specifically *Encarsia formosa*.

## Project Overview

Miniature insects operate in a low Reynolds number aerodynamic regime (Re ≈ 10) where viscous forces dominate inertial forces, rendering conventional pressure-based lift ineffective. To generate sufficient lift, these insects rely on a clap-and-fling wing kinematics mechanism.

* **Clap Phase:** Wings approach each other at the top of the stroke, expelling fluid downward and building circulation.


* **Fling Phase:** Wings rotate apart, drawing fluid into the gap and creating strong leading-edge vortices that enhance lift.



This project uses a robotic model to replicate this motion in a high-viscosity glycerin-water solution to study the resulting aerodynamic forces and validate findings against existing literature.

---

## Experimental Parameters


**Wing span** 90 mm

 
**Mean chord** 45 mm (aspect ratio = 2)

 
**Wing thickness** 2.5 mm (polycarbonate)

 
**Initial gap**  ~4–5 mm (≈ 0.1c)

 
**Wing translation velocity**  ~0.19 m/s

 
**Fluid medium**  Glycerin–water solution

 
**Kinematic viscosity**  ≈ 860 mm²/s

 
**Target Reynolds number**  Re ≈ 10

 
**Stroke period**  ~2 seconds per full cycle

 

---

## Mechanical and Hardware Design

The system relies on a structural metal frame mounted over a glass tank to ensure precise alignment and minimize vibration. The transmission and motion system includes:

* **Transmission:** Each wing is mounted on a D-profile shaft driven by a 90° bevel gear pair (1:1 ratio) connected to a NEMA-17 stepper motor.


* **Translation:** A pinion gear (module 2, 18 teeth) engages a linear rack to convert rotation into linear translation, supported by MGN15 linear guide rails.


* **Control Hardware:** An Arduino Mega coordinates the motion, driving four TB6600 microstepping drivers (one for each motor) to control translation and rotation independently.



---

## Software and Motion Control

The motion control code is written for the Arduino Mega using the `AccelStepper` library for simultaneous, non-blocking control. The motion profile includes a 50% overlap between rotation and translation during the fling phase, and a 100% overlap during the clap phase.

```cpp
#include <AccelStepper.h>

// Motor definitions (STEP pin, DIR pin)
AccelStepper motor1(AccelStepper::DRIVER, 2, 3); // Translation left 
AccelStepper motor2(AccelStepper::DRIVER, 4, 5); // Translation right 
AccelStepper motor3(AccelStepper::DRIVER, 6, 7); // Rotation left 
AccelStepper motor4(AccelStepper::DRIVER, 8, 9); // Rotation right

int translationSteps = 750; // ~45 mm stroke 
int rotationSteps = 200; // ~45 deg rotation 

void setup() {
  motor1.setMaxSpeed(1000); 
  motor2.setMaxSpeed(1000); 
  motor3.setMaxSpeed(800);
  motor4.setMaxSpeed(800); 
  motor1.setAcceleration(500);
  motor2.setAcceleration(500); 
  motor3.setAcceleration(400); 
  motor4.setAcceleration(400);
}

void loop() {
  // -- Fling phase --
  motor3.move(rotationSteps); 
  motor4.move(-rotationSteps); 
  motor1.move(translationSteps);
  motor2.move(-translationSteps); 
  runAllMotors(); 
  delay(300); 

  // -- Clap phase --
  motor1.move(-translationSteps);
  motor2.move(translationSteps); 
  motor3.move(-rotationSteps); 
  motor4.move(rotationSteps); 
  runAllMotors(); 
  delay(500); 
}

```

*(Note: A custom `runAllMotors()` function is utilized to execute the stepped movements for all coordinated motors)*.

---

## Current Status & Future Work

**Current Milestones:**

* Completed the full mechanical assembly of the rack-and-pinion, bevel gear, and frame system.


* Successfully implemented the Arduino Mega control code and verified correct motor responses.


* Performed dry-run motion tests in the air, confirming correct fling and clap kinematics without mechanical binding.



**Next Steps:**

* Install precision-cut polycarbonate wings.


* Prepare the glycerin-water mixture to achieve the target kinematic viscosity of ≈ 860 mm²/s.


* Integrate force sensors (load cells or strain gauges) to measure aerodynamic drag and lift forces.


* Compare experimental force coefficients against published data by Ford et al. (2019) to validate the model.



---

## References

* Ford, M. P., Kasoju, V. T., Gaddam, M. G., & Santhanakrishnan, A. (2019). Aerodynamic effects of varying solid surface area of bristled wings performing clap and fling. *Bioinspiration & Biomimetics*, 14, 046003.
