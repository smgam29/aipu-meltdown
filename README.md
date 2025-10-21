#AIPU Meltdown: A Small AI Game**

AIPU Meltdown is a single-page web game. Players must maintain a critical balance between the AIPU's **Heat** (which rises with computation load) and **Stability** (which degrades due to instability events), all while managing the audio output of the processing unit.

This project is unique in that it is built entirely within a single .html file, containing all HTML structure, JavaScript logic, and CSS styling.

## **✨ Features**

* **Real-time Canvas Rendering:** Visual representation of the AIPU's core, heat bar, and stabilization metrics using the Canvas API.  
* **Reactive Heat Mechanics:** The central **Heat** resource increases over time and must be managed by the player via active cooling.  
* **Stability Jitter:** A background instability mechanic that randomly degrades core performance unless actively countered.  
* **Procedural Audio Engine (Tone.js):** Uses Tone.js to generate the ambient processing sound and error tones, tied directly to the game state (e.g., sound pitch and speed change with **Heat** level).  
* **Single-File Architecture:** All components (HTML, JS, CSS) are consolidated for easy deployment.  
* **Persistent User Data (Firebase):** Automatically saves the highest recorded **Heat Level** for the current user using Firestore.

## **🛠️ Technology Stack**

| Technology | Purpose | Notes |
| :---- | :---- | :---- |
| **HTML5** | Structure | Single-file wrapper. |
| **Tailwind CSS** | Styling | Loaded via CDN for rapid utility-first styling. |
| **JavaScript (ES6+)** | Game Logic | Handles the game loop, physics, input, and state management. |
| **Tone.js** | Audio Synthesis | Used for all in-game procedural sound generation. |
| **Firebase SDK** | Persistence | Firestore for saving/loading user scores. |

## **⚙️ Game Tuning Tables**

The game's difficulty and sensory feedback are controlled by four main constants objects within the JavaScript code. Modifying these values is the primary way to adjust the gameplay experience.

### **1\. Difficulty Escalation (DIFFICULTY\_LEVELS)**

This table defines the state changes that occur as the player's **Heat Level** increases. Each level is triggered when the currentHeat surpasses the triggerHeat value.

| Level | triggerHeat | baseHeatIncreaseRate | maxJitterThreshold (px) | jittersPerSecond (max) | Effect on Player |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **1** | 0.0 | 0.0005 | 4 | 0.25 | Stable, low ambient temperature. |
| **2** | 0.4 | 0.0010 | 8 | 0.50 | Moderate increase in heat generation. |
| **3** | 0.7 | 0.0018 | 15 | 0.75 | High heat generation; frequent, short jitters. |
| **4** | 0.9 | 0.0030 | 25 | 1.00 | Critical heat generation; high risk of long-duration jitters. |

*Note: maxJitterThreshold is the maximum displacement allowed when applying visual canvas jitter.*

### **2\. Procedural Audio Synthesis (TONE\_SETTINGS)**

This table maps the **Heat Level** to the fundamental parameters of the Tone.js synthesizers, providing general feedback.

| State | Target (Synth/Tempo) | Parameter | Low Heat (Level 1\) | High Heat (Level 4\) | Effect on Sound |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Ambient Tone** | synth.frequency.value | Frequency (Hz) | 120 | 440 | Pitch rises, indicating higher core load. |
| **Ambient Tone** | vibrato.frequency.value | Vibrato Speed (Hz) | 0.5 | 5.0 | Sound "shimmers" more rapidly with heat. |
| **Core Processing** | Tone.Transport.bpm.value | Tempo (BPM) | 60 | 180 | The rhythm of the background "processing pulse" speeds up. |
| **Error Tone** | freq\_error | Frequency (Hz) | 300 | 600 | Error beep pitch is higher when core is hot. |

### **3\. Audio Layer Escalation (TONE\_LAYERS)**

This defines which instruments and sound effects become audible as the AIPU enters higher difficulty states.

| Level | Sound Layer Introduced | Tone.js Component | Purpose |
| :---- | :---- | :---- | :---- |
| **1** | **Core Ambient Hum** | FMSynth | Steady, low-frequency hum representing baseline power. |
| **2** | **Processing Pulse** | Loop (using MembraneSynth) | Regular, rhythmic beat symbolizing active computation cycles. |
| **3** | **Instability Warning** | PolySynth with Delay | Eerie, high-pitched pad that swells with rising instability. |
| **4** | **Critical Overload Alarm** | NoiseSynth | Fast, abrasive noise bursts triggered during critical heat spikes. |

### **4\. Visual Feedback & Aesthetics (CANVAS\_AESTHETICS)**

This outlines the visual changes applied to the canvas and core rendering based on the current heatLevel.

| Level | Core Color (fill/stroke) | Jitter Intensity (Canvas Shake) | Screen Filter / Overlay |
| :---- | :---- | :---- | :---- |
| **1** | \#42A5F5 (Blue) / White | Minimal (0-5px) | None / Clean Interface |
| **2** | \#FFC107 (Yellow-Orange) | Low (5-10px) | Subtle Scanline Overlay (CSS Filter) |
| **3** | \#E53935 (Bright Red) | Moderate (10-20px) | Blinking Glitch Overlay, increasing canvas opacity fades. |
| **4** | \#B71C1C (Dark Crimson) | High (20-40px) | Full Screen VHS Glitch and heavy radial distortion. |

## **🚀 Getting Started**

### **Prerequisites**

1. A modern web browser (Chrome, Firefox, Edge).  
2. A local server environment (optional, but recommended for development).

### **Installation and Run**

1. **Clone the Repository:**  
   git clone your-repo-url  
   cd aipu-control-simulation

2. **Open the File:** The entire application resides in one file: index.html. Open this file directly in your web browser.

**Note:** The Firebase configuration and initial user authentication tokens are expected to be injected via global JavaScript variables (\_\_app\_id, \_\_firebase\_config, \_\_initial\_auth\_token) by the hosting environment. If running standalone, these variables must be mocked or replaced with valid credentials from a Firebase project setup.

### **Essential Global Variables (Provided by Hosting Environment)**

The following global variables are critical for Firebase functionality:

| Variable | Description |
| :---- | :---- |
| \_\_app\_id | Unique application ID used for Firestore data path isolation. |
| \_\_firebase\_config | JSON string containing Firebase initialization configuration. |
| \_\_initial\_auth\_token | A custom Firebase auth token used for initial user sign-in via signInWithCustomToken. If undefined, the app falls back to signInAnonymously. |

## **📂 Data Structure (Firestore)**

User data is stored privately using the authenticated userId.

**Path:** /artifacts/{appId}/users/{userId}/gameData/aipu\_stats

| Field | Type | Description |
| :---- | :---- | :---- |
| highestLevel | Number | The highest Heat Level reached by the user. |
| timestamp | Timestamp | Last time the score was updated. |

