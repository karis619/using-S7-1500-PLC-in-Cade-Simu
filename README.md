# using-S7-1500-PLC-in-Cade-Simu
this project demonstrates how to simulate Siemens SIMATIC S7-1500 Digital Input/Digital Output (DI/DO) module in CADe_SIMU.

# Multi-Motor Sequence Control System with Emergency Interlock

An industrial automation and PLC control simulation implementing sequential motor starting, interlocking logic, and visual status signaling. Designed in **CADe_SIMU** using a simulated **Siemens SIMATIC S7-1500 DI/DO** controller with complementary ladder logic.

---

## 📌 Project Overview

This project implements a multi-station industrial control circuit designed to govern two 3-phase induction motors ($M_1$ and $M_2$) and three signaling pilot lamps (Red, Yellow/White, and Green). The control scheme supports dual operating states triggered by separate operator push buttons, backed by a master emergency stop switch that halts all active outputs immediately upon opening.

---

## ⚙️ Operational Specifications

1. **Emergency Stop (`SWB`):**
   * Acts as the master safety interlock.
   * Opening `SWB` immediately unlatches all active control relays, shutting down all running motors and extinguishing all pilot lamps.

2. **State 1 — Red Push Button (`PBR`):**
   * Pressing momentary push button `PBR` latches internal control relay `CR1`.
   * Energizes the **Red Pilot Light** (`Red-PLT` / `Q0.2`).
   * Starts **Motor 1** (`M1` / `Q0.0`).
   * Maintained via self-latching branch until `SWB` is opened.

3. **State 2 — Green Push Button (`PBG`):**
   * Pressing momentary push button `PBG` latches internal control relay `CR2`.
   * Energizes both the **White/Yellow Pilot Light** (`White-PLT` / `Q0.3`) and the **Green Pilot Light** (`Green-PLT` / `Q0.4`).
   * Runs both **Motor 1** (`M1` / `Q0.0`) and **Motor 2** (`M2` / `Q0.1`).
   * Maintained via self-latching branch until `SWB` is opened.

---

## 🧠 Ladder Logic Diagram

```text
 L1 (+24V)                                                        L2 (0V)
---|-------------------------------------------------------------------|---
   | Rung 1: Latch State 1 (CR1) via PBR                               |
0  |      SWB             PBR                                   CR1    |
   +------[ ]------+------[ ]-----------------------------------( )----+
   |               |                                                   |
   |               |      CR1                                          |
   |               +------[ ]------+                                   |
   |                                                                   |
   | Rung 2: Latch State 2 (CR2) via PBG                               |
1  |      SWB             PBG                                   CR2    |
   +------[ ]------+------[ ]-----------------------------------( )----+
   |               |                                                   |
   |               |      CR2                                          |
   |               +------[ ]------+                                   |
   |                                                                   |
   | Rung 3: Red Pilot Light                                           |
2  |      CR1                                                 Q0.2     |
   +------[ ]---------------------------------------------------( )----+
   |                                                                   |
   | Rung 4: White/Yellow Pilot Light                                  |
3  |      CR2                                                 Q0.3     |
   +------[ ]---------------------------------------------------( )----+
   |                                                                   |
   | Rung 5: Green Pilot Light                                         |
4  |      CR2                                                 Q0.4     |
   +------[ ]---------------------------------------------------( )----+
   |                                                                   |
   | Rung 6: Motor 1 Contactor (M1 runs in State 1 OR State 2)         |
5  |      CR1                                                 Q0.0     |
   +------[ ]------+--------------------------------------------( )----+
   |               |                                                   |
   |      CR2      |                                                   |
   +------[ ]------+                                                   |
   |                                                                   |
   | Rung 7: Motor 2 Contactor (M2 runs in State 2 only)               |
6  |      CR2                                                 Q0.1     |
   +------[ ]---------------------------------------------------( )----+
---|-------------------------------------------------------------------|---
