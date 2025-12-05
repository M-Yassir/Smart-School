# 📄 Technical Report: Smart School IoT Infrastructure

**Project Name:** SecureSmart School System
**Environment:** Cisco Packet Tracer Simulation
**Topology:** Star Network with Zone-Based Automation
**Core Protocols:** MQTT (Telemetry), HTTP (Control), RFID (Security)

## 1\. Executive Summary

This project simulates a next-generation Smart School environment divided into five functional zones: Administration, Front Door, Classroom, Hallway (Couloir), and Restrooms (Toilet). The system integrates safety (Fire/Smoke detection), security (RFID Access & Surveillance), and environmental comfort (HVAC & Lighting) into a unified network managed by a central Administration Hub.

## 2\. Zone Architecture

### 2.1 Zone A: Administration (The Control Center)

This zone hosts the network backbone and management interfaces.

  * **Hardware:**
      * **Home Gateway (DLC-100):** Acts as the central wireless hub, DHCP server, and IoT Registration Server for the Classroom and Hallway components.
      * **MQTT Broker (Server):** The destination for telemetry data sent from the SBC in the Toilet zone. IP: `192.168.25.25`.
      * **Access Control Server:** Validates RFID credentials.
      * **Smartphone:** Used by administrators to visualize the "IoT Monitor" dashboard.

### 2.2 Zone B: Front Door (Security Checkpoint)

  * **Function:** Restricts entry to authorized staff/students.
  * **Components:**
      * **RFID Reader:** Scans physical cards.
      * **Electric Door Lock:** Unlocks only upon receiving a "Valid" signal from the Authentication Server.
      * **Test Subjects:** 1 Valid Card (Grant Access) and 1 Invalid Card (Deny Access).

### 2.3 Zone C: The Classroom (Comfort & Safety)

  * **Function:** Ensures an optimal learning environment.
  * **Automation Logic (Gateway Rules):**
      * **Thermostat:** Constantly monitors room temperature.
      * **Air Conditioner:** Activates if Temp \> 25°C.
      * **Ceiling Fan:** Activates if Temp \> 22°C (Low speed).
      * **Fire Safety:** Smoke Detector linked to a local Sprinkler and Siren.

### 2.4 Zone D: The "Couloir" / Hallway (Transit)

  * **Function:** Energy saving and surveillance.
  * **Automation Logic:**
      * **Motion Detector + Light:** The hallway lights remain OFF to save energy until the Motion Detector senses a person, triggering the Light to ON.
      * **Webcam:** Provides a live video feed to the Administration Smartphone for security monitoring.
      * **Fire Safety:** Redundant Smoke Detector and Siren coverage.

### 2.5 Zone E: The Toilet (High-Tech Safety Node)

  * **Controller:** **Single Board Computer (SBC)** running Python.
  * **Function:** This is the most advanced node, running the custom Python script we developed. It handles fire safety and transmits data via MQTT.
  * **Components (Interconnected to SBC):**
      * **Fire Monitor (Input):** Analog smoke sensing.
      * **Actuators:** Sprinkler, Siren, Piezo Speaker, Window (Ventilation), and Light.
  * **Logic:**
      * Local processing of smoke levels.
      * Automatic triggering of all interconnected actuators during an emergency.
      * **MQTT Telemetry:** Sends critical alerts (`{"alert": "CRITICAL", "smoke_level": 1023}`) to the Administration Server.

## 3\. Communication Diagram

  * **RFID System:** Uses HTTP/SQL queries to the Registration Server.
  * **Class/Couloir:** Uses the "IoE Protocol" to communicate state changes to the Home Gateway.
  * **Toilet SBC:** Uses **TCP/IP & MQTT** packets to publish data to the Broker.

## 🛠️ Technical Stack

| Component | Protocol/Tech | Description |
| :--- | :--- | :--- |
| **SBC (Toilet)** | **Python + MQTT** | Custom coded logic for threshold-based fire detection. |
| **Front Door** | **RFID** | Database-driven access control. |
| **Automation** | **IoE Rules** | If/Then logic stored in the Home Gateway for HVAC/Lights. |
| **Network** | **DHCP/TCP** | Dynamic IP assignment for all nodes. |

---

## 🚦 How to Run the Simulation

1.  **Open Project:** Load the `.pkt` file in Cisco Packet Tracer.
2.  **Test Access:**
    * Swipe the **Valid Card** at the Front Door -> **Door Opens**.
    * Swipe the **Invalid Card** -> **Door stays Locked**.
3.  **Test Automation:**
    * **Alt+Click** the Motion Detector in the Couloir -> **Light turns ON**.
4.  **Test Fire System (SBC):**
    * **Manual Test:** Drag the **Fire Thing** (Simulated Fire Source) close to the Smoke Detector in the Toilet.
    * Observe: **Siren sounds**, **Window opens**, **LCD flashes**.
    * Check the **MQTT Broker** or Admin Laptop to see the incoming JSON alert:
        ```json
        {"alert": "CRITICAL", "location": "Toilet", "value": 1023}
        ```
