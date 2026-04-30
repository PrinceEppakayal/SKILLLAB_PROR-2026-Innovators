# SKILL LAB PRACTICAL HACKATHON 

## 1. Team Identity 

### 1.1 Studio / Group Name 
**Innovators**

### 1.2 Team Members 

| Name | Primary Role | Secondary Role | Strengths Brought to the Project |
| :--- | :--- | :--- | :--- |
| **Soham Mayekar** | Hardware Block Design / Vivado | Software | FPGA Architecture, AXI Interconnects, Presenting |
| **Prince Eppakayal** | Software / Vitis C Code | Documentation | Bare-metal C programming, Sensor Logic |
| **Swarnima Jahagirdar** | Debugging, Testing | Documentation | Wiring, Sensor Calibration, Hardware Debugging |
| **Priyanka Tiwari** | Integration | | Integration of hardware and software modules |

### 1.3 Project Title 
**"Autonomous Paint Booth Safety & Quality Controller"**

### 1.4 One-Line Pitch 
A System-on-Chip (SoC) embedded controller utilizing true hardware concurrency to ensure quality and safety in highly volatile automotive paint booths. 
 
### 1.5 Expanded Project Idea 
In modern automotive manufacturing, automated paint booths are highly volatile environments requiring strict atmospheric control for paint adherence and posing explosive hazards due to Volatile Organic Compounds (VOCs). This project implements an Autonomous Paint Booth Safety & Quality Controller utilizing a Spartan-7 FPGA. 

By employing a Hardware/Software Co-design methodology, we synthesized a custom System-on-Chip (SoC) centered around the Xilinx MicroBlaze soft processor. Dedicated hardware IP blocks handle the strict microsecond timing and digital safety interrupts, while the MicroBlaze processor acts as a deterministic decision engine. The system continuously polls kinematic data (MPU6050) to predict robotic arm failures, monitors curing temperatures (DHT11), and utilizes a zero-latency digital hardware interrupt from the gas sensor (MQ135) to instantly halt the system if an explosive threshold is reached.

---

## 2. Inspiration 

### 2.1 References 

| Source Type / Link | Title | What Inspired You |
| :--- | :--- | :--- |
| [Web Link](https://www.osha.gov/spray-operations/standards) | **OSHA Industrial Paint Booth Safety Guidelines** | The strict requirement for instantaneous ventilation and power-cutoff during VOC leaks. |
| [Video Link](https://youtu.be/1prlAjndla8?si=bT3wWNWVGYPbZMcQ) | **Hardware/Software Co-Design in FPGAs** | Visualizing the kinematics of an industrial painting robot and its enclosed environment, justifying the need for our MPU6050 and MQ135 safety interlocks. |

### 2.2 Original Twist 
Unlike standard microcontroller projects that poll sensors sequentially in a "while" loop (introducing dangerous software latency), our twist is **"Zero-Latency Hardware Interlocks."** We decoupled the explosive gas detection from the main processor's math loop. By routing the MQ135's digital interrupt pin through an AXI GPIO directly to the MicroBlaze, we guarantee an immediate, deterministic hardware halt the millisecond a VOC leak occurs, simulating true industrial-grade safety. 

---

## 3. Project Intent 

### 3.1 User Journey 
A factory supervisor powers on the system, and the Spartan-7 FPGA configures the MicroBlaze SoC. The supervisor monitors the serial dashboard as the robotic arm begins painting. The DHT11 verifies the booth is at an optimal 22°C for the clear coat to cure. Suddenly, the exhaust fan fails, and VOCs from the paint thinner rapidly accumulate in the booth. Before the supervisor can react, the MQ135 detects the lower explosive limit. Instantly, the hardware comparator sends a digital interrupt via AXI GPIO. The MicroBlaze immediately cuts the power relay to the robotic arm, preventing a spark and an explosion, while flashing a critical system halt alert. 

---

## 4. Definition of Success 

### 4.1 Definition of “Usable” 
The system successfully synthesizes the bitstream, the MicroBlaze runs the C application, and all three sensors (DHT11, MQ135, MPU6050) report accurate data to the serial monitor without the I2C or AXI buses crashing. 

### 4.2 Minimum Usable Version 
The MQ135 and DHT11 operating concurrently. If the digital pin on the MQ135 goes HIGH, the MicroBlaze instantly triggers a "HALT" state on an output LED/Relay. 

### 4.3 Stretch Features 
Incorporating an Ultrasonic sensor and an IR sensor to act as secondary spatial safety checks, preventing the robotic arm from colliding with the vehicle chassis or entering human blind spots. 

---

## 5. System Overview 

### 5.1 Project Type 
- [x] Electronics-based 
- [ ] Mechanical 
- [x] Sensor-based 
- [x] App-connected (Serial/UART Dashboard) 
- [ ] Motorized 
- [ ] Sound-based 
- [ ] Light-based (Status LEDs) 
- [ ] Screen/UI-based 
- [ ] Fabricated structure 
- [x] Game logic based (State Machine Logic) 
- [ ] Installation 
- [ ] Other: 

### 5.2 High-Level System Description 
* **Inputs:** DHT11 (Temperature/Humidity via 1-wire), MQ135 (VOC Gas via Digital Comparator), MPU6050 (Kinematics via I2C). 
* **Processing:** A Spartan-7 FPGA running a custom MicroBlaze soft-processor. Hardware IP blocks handle the electrical protocols (AXI IIC, AXI GPIO). The MicroBlaze evaluates the states deterministically in a bare-metal C application.
* **Output:** Physical GPIO outputs mapped to LEDs indicating SYSTEM SAFE, QUALITY WARNING, or EMERGENCY HALT. 

### 5.3 Input / Output Map 

| System Part | Type | What It Does |
| :--- | :--- | :--- |
| **DHT11** | Input | Monitors booth temp/humidity for paint curing quality |
| **MQ135 (Digital Pin)** | Input | Hardware comparator that goes HIGH if explosive gas limits are reached |
| **MPU6050** | Input | Monitors robotic arm for micro-vibrations indicating mechanical failure |
| **AXI GPIO / LEDs** | Output | Triggers physical safety relays/indicators based on system state |
| **MicroBlaze UART** | Output | Transmits real-time system status to the supervisor's laptop |

---

## 6. System Design, Sketches and Visual Planning 

### 6.1 Concept Architecture / Schematic

![Concept Architecture](images/image_1.png)

*(Note: Make sure your image file is named `image_1.png` and placed inside an `images` folder in your repository for the link to display correctly.)*
