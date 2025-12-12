# 📄 Technical Report: Smart School IoT Infrastructure

**Project Name:** SecureSmart School System

**Environment:** Cisco Packet Tracer Simulation

**Topology:** Star Network with Zone-Based Automation & Clustered Management

**Core Protocols:** MQTT (Telemetry), HTTP (Control), RFID (Security)

## 1. Executive Summary

This project simulates a next-generation Smart School environment divided into five functional zones: Administration, Front Door, Classroom, Hallway (Transit), and Restrooms (Toilet). The system integrates safety (Fire/Smoke detection), security (RFID Access & Surveillance), and environmental comfort (HVAC & Lighting) into a unified network. Key infrastructure components are organized into logical **Clusters** for cleaner management.

## 2. Zone Architecture

### 2.1 Zone A: Administration (The Control Center)

This zone hosts the network backbone and management interfaces, organized into functional clusters.

* **Cluster 1: IoT Management System & MQTT Server**
    * **Home Gateway (DLC-100):** Acts as the central wireless hub, DHCP server, and IoT Registration Server.
    * **MQTT Broker (Server):** The destination for telemetry data sent from the SBC in the Toilet zone. IP: `192.168.25.25`.
* **Cluster 2: Door Locking System**
    * **Access Control Server:** Validates RFID credentials against a database to grant or deny entry.
* **Local Comfort:**
    * **Thermostat & Fan:** Automated cooling system for the administration office.
* **Monitoring:**
    * **Smartphone:** Used by administrators to visualize the "IoT Monitor" dashboard and webcam feeds.

### 2.2 Zone B: Front Door (Security Checkpoint)

* **Function:** Restricts entry to authorized staff/students.
* **Components:**
    * **RFID Reader:** Scans physical cards.
    * **Electric Door Lock:** Unlocks only upon receiving a "Valid" signal from the Authentication Server.
    * **Test Subjects:** 1 Valid Card (Grant Access) and 1 Invalid Card (Deny Access).

### 2.3 Zone C: The Classroom (Comfort & Safety)

* **Function:** Ensures an optimal learning environment with automated safety overrides.
* **Sensors:**
    * **Temperature Monitor:** Real-time thermal tracking.
    * **Humidity Monitor:** Tracks air moisture levels.
    * **Smoke Detector:** Monitors for fire hazards.
* **Actuators:**
    * **Thermostat & Air Conditioner:** Automates climate control based on sensor readings.
    * **Siren:** Audio alarm for fire events.
    * **Window:** **Safety Override** — Automatically opens to ventilate smoke if the Smoke Detector triggers.

### 2.4 Zone D: The Transit / Hallway (Surveillance)

* **Function:** Energy saving and security surveillance.
* **Automation Logic:**
    * **Motion Detector + Light:** "Lights on Demand", The hallway lights remain OFF to save energy until the Motion Detector senses a person, triggering the Light to ON.
    * **Webcam:** Provides a live video feed to the Administration Smartphone for security monitoring.
* **Fire Safety:**
    * **Redundant Smoke Detector & Siren:** Provides coverage for the transit area.
    * **Simulation Object:** **Old Car** — Used to simulate exhaust/smoke for testing detector sensitivity.

### 2.5 Zone E: The Toilet (High-Tech Safety Node)

* **Controller:** **Single Board Computer (SBC)** running a custom Python script.
* **Function:** This is the most advanced node, handling complex logic for fire safety, occupancy, and MQTT telemetry.
* **Components (Interconnected to SBC):**
    * **Inputs:**
        * **Fire Monitor:** Analog smoke sensing.
        * **Motion Detector:** Detects user presence.
    * **Outputs (Actuators):**
        * **Sprinkler:** Active fire suppression.
        * **Siren & Piezo Speaker:** Dual-tone audio warning.
        * **Window:** Opens for ventilation during emergencies.
        * **LCD Screen:** Displays status text ("System Safe" vs "FIRE DETECTED").
        * **Light:** Activates automatically when motion is detected.
* **Logic:**
    * **Occupancy:** Motion detected -> Light ON.
    * **Emergency:** Smoke detected -> Sprinkler ON, Siren ON, Window OPEN, MQTT Alert SENT.
* **Simulation Object:** **Fire Thing** — Drag-and-drop object to test the Fire Monitor logic.

## 3. Communication Diagram

* **RFID System:** Uses HTTP/SQL queries to the Registration Server.
* **Class/Transit:** Uses the "IoE Protocol" to communicate state changes to the Home Gateway.
* **Toilet SBC:** Uses **TCP/IP & MQTT** packets to publish JSON data to the Broker.

## 🛠️ Technical Stack

| Component | Protocol/Tech | Description |
| :--- | :--- | :--- |
| **SBC (Toilet)** | **Python + MQTT** | Custom coded logic for threshold-based fire detection & occupancy. |
| **Front Door** | **RFID** | Database-driven access control. |
| **Automation** | **IoE Rules** | If/Then logic stored in the Home Gateway for HVAC/Lights. |
| **Network** | **DHCP/TCP** | Dynamic IP assignment for all nodes. |

---

## 🚦 How to Run the Simulation

1.  **Open Project:** Load the `.pkt` file in Cisco Packet Tracer.
2.  **Test Access:**
      * Swipe the **Valid Card** at the Front Door -\> **Door Opens**.
      * Swipe the **Invalid Card** -\> **Door stays Locked**.
3.  **Test Transit Automation:**
      * **Alt+Click** the Motion Detector in the Transit area -\> **Light turns ON**.
      * Move the **Old Car** near the Transit Smoke Detector to test the alarm.

4. **Test Fire System (SBC - Toilet) Setup**

This is a multi-step process for administrators to subscribe to the SBC's alerts.

-  Configure MQTT Broker:
      * Access the **Server** (MQTT Broker).
      * Go to the **Services** tab, then select **MQTT**.
      * Define the authentication credentials (e.g., User: `admin`, Password: `admin`).
      * Ensure the MQTT Service is **ON**.
-  Configure Admin Monitor:
      * Access the **Smartphone** (Admin Monitor).
      * Go to the **Desktop** tab, then select **IoT Monitor**.
      * **Broker IP:** Enter `192.168.25.25`.
      * **Credentials:** Enter the configured User (`admin`) and Password (`admin`).
      * **Subscribe:** Click the **Subscribe** button and enter the topic `home/fire_system`.
-  Manual Test (Fire Simulation):
      * Drag the **Fire Thing** (Simulated Fire Source) close to the Fire Monitor in the Toilet.
      * **Observe (Local Actuators):** Siren sounds, Piezo Speaker buzzes, Window opens, LCD updates to **"FIRE DETECTED"**, Sprinkler activates.
      * **Remote Check (Admin Smartphone):** Wait a few moments; the incoming JSON alert will appear under the `home/fire_system` topic:
        ```json
        {"alert": "CRITICAL", "location": "Toilet", "value": 1023}
        ```

5. **Test Classroom Safety**

  * **Trigger:** Use **Alt+Click** on the Smoke Detector in the Classroom.
  * **Verify Automation:** Verify the **Window Opens** automatically to vent smoke.
  * **Verify Alarm:** Verify the **Siren** activates as an auditory warning.
