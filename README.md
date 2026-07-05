# 🤖 Obstacle Avoiding Robot (Arduino)

An autonomous obstacle-avoidance robot built with an Arduino, an ultrasonic distance sensor mounted on a servo, and an L298N motor driver. The robot drives forward until it detects an obstacle within range, then scans left and right to pick the clearer path and turns accordingly.

## Features

- Continuous obstacle detection using an HC-SR04 ultrasonic sensor
- Servo-mounted sensor "head" that scans left/right (like a radar sweep) when blocked
- Automatic decision-making: reverses, checks both directions, and turns toward the side with more clearance
- Simple two-motor differential drive control via L298N H-bridge driver
- Serial output of live distance readings for debugging (9600 baud)

## How It Works

1. On startup, the servo centers to 90° and takes a few initial distance readings to stabilize the sensor.
2. In the main loop, the robot moves forward as long as the distance ahead is greater than **35 cm**.
3. When an obstacle is detected within 35 cm:
   - The robot stops and reverses briefly.
   - It stops again, then sweeps the servo to look **right** (10°) and **left** (170°), taking a distance reading at each position.
   - It compares the two readings and turns toward whichever side has more open space.
4. The loop repeats, continuously polling the sensor.

## Hardware Components

| Component | Quantity | Notes |
|---|---|---|
| Arduino Uno (or compatible) | 1 | Main controller |
| L298N Motor Driver Module | 1 | Dual H-bridge for two DC motors |
| HC-SR04 Ultrasonic Sensor | 1 | Distance measurement |
| SG90 (or similar) Micro Servo | 1 | Rotates the ultrasonic sensor |
| DC Gear Motors | 2 | Left and right drive wheels |
| Robot chassis + wheels | 1 set | 2WD or 4WD chassis |
| Battery pack (e.g., 7.4V–12V) | 1 | Powers motors + Arduino |
| Jumper wires | Several | Connections |

## Pin Connections

### Motor Driver (L298N) → Arduino

| L298N Pin | Arduino Pin |
|---|---|
| IN1 (Left Motor Forward) | D5 |
| IN2 (Left Motor Backward) | D4 |
| IN3 (Right Motor Forward) | D3 |
| IN4 (Right Motor Backward) | D2 |

> Connect L298N `ENA`/`ENB` enable pins to 5V (or PWM pins if you want speed control) and wire the motor outputs (`OUT1–OUT4`) to your left/right DC motors.

### Ultrasonic Sensor (HC-SR04) → Arduino

| HC-SR04 Pin | Arduino Pin |
|---|---|
| Trig | A3 |
| Echo | A2 |
| VCC | 5V |
| GND | GND |

### Servo Motor → Arduino

| Servo Wire | Arduino Pin |
|---|---|
| Signal | D11 |
| VCC | 5V |
| GND | GND |

> ⚠️ Power note: Driving motors and servo directly off the Arduino's 5V pin can cause brownouts. Power the motors from the battery pack via the L298N, and consider a separate 5V supply for the servo on larger builds.

## Software Dependencies

- [Arduino IDE](https://www.arduino.cc/en/software)
- **Servo** library (built into the Arduino IDE)
- **NewPing** library — install via Arduino IDE: `Sketch > Include Library > Manage Libraries...` → search "NewPing"

## Getting Started

1. Install the Arduino IDE and the NewPing library (see above).
2. Wire the components as described in the pin tables.
3. Open `obstacle_avoiding_robot.ino` in the Arduino IDE.
4. Select your board and port under `Tools`.
5. Upload the sketch.
6. Open the Serial Monitor (9600 baud) to view live distance readings.
7. Power the robot from the battery pack and place it on the floor to test.

## Configuration

You can tune robot behavior by adjusting these values in the code:

- `maximum_distance` — max range (cm) the ultrasonic sensor will report (default: 200)
- Obstacle threshold — the `distance <= 35` check in `loop()` controls how close an object can get before the robot reacts
- Turn duration — the `delay(350)` in `turnRight()`/`turnLeft()` controls how sharp/long each turn is
- Servo scan angles — `10°` (right) and `170°` (left) in `lookRight()`/`lookLeft()`

## Possible Improvements

- Add PWM speed control via the L298N enable pins for smoother acceleration
- Add a third ultrasonic sensor or IR sensors for side-facing detection
- Replace fixed delays with non-blocking timing (`millis()`) for more responsive behavior
- Add a buzzer/LED to indicate detected obstacles
- Log sensor data to build a simple map of the surroundings

## License

This project is open source. Consider adding an [MIT License](https://choosealicense.com/licenses/mit/) if you want others to freely use/modify it.


