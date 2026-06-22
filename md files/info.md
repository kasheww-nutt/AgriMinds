# FarmWise App Overview & Technical Architecture

Welcome to the **FarmWise** technical and functional blueprint. FarmWise is a state-of-the-art dual-platform application engineered to empower the agricultural community. It bridges the gap between rural farmers, specialized agricultural experts, and metropolitan consumers. 

This document provides a comprehensive breakdown of the application modules, their workflows, and architecture for both the **Web Platform (React + TS + Tailwind)** and the **Native Mobile Platform (Android + Jetpack Compose + Kotlin)**.

---

## 🚀 Architectural Vision

FarmWise features a unique dual-architecture pattern:
1. **Native Client Framework (`/app`):** Built with **Kotlin, Jetpack Compose, Material Design 3, Coroutines**, and native hardware integrations (CameraX APIs).
2. **Interactive Simulator Client Framework (`/web`):** Developed in **React 19, TypeScript, and Tailwind CSS**. This handles real-time previews, simulated Android container interactions, and proxies operations directly.

---

## 🛠️ Core Features & How They Work

### 1. Multi-User Authentication & Role Selector (`Login.tsx` / `LoginActivity`)
The gateway to FarmWise is an adaptive, role-based login shield. It configures the user's localized session parameters.
* **How It Works:**
  * **Role Allocation:** Users specify their role as a **Farmer** (access back to diagnostics, government schemes), an **Expert** (access to consult reports), or a **Consumer** (gains entrance to buy farm-fresh organic goods).
  * **Location Determination:** Users input their state/region, which seeds the localized marketplace databases and weather APIs.
  * **Language Selection:** Fully configures the internationalization context (`en` for English, `hi` for Hindi, `te` for Telugu, `es` for Spanish, etc.) across the entire lifecycle.

---

### 2. Crop Diagnostics Engine (`CameraCapture.tsx` & `AnalysisResult.tsx` / `MainActivity.kt`)
The crowning feature of FarmWise is the **AI-powered plant disease diagnosis** module.
* **Under the Hood with Gemini:**
  * A picture of the crop is acquired (using live CameraX feeds on Android, or camera streams and curated test assets on Web).
  * The image is converted into a base64 binary payload and delivered server-side to the Gemini API (`gemini-2.5-flash` model).
  * **The Structured prompt** instructs Gemini to return a high-fidelity JSON object containing the botanical name, detected pathological condition, severity details, localized organic and chemical remedy packages, and structural confidence indices.
* **The Diagnostic Interface:**
  * Displays color-coded symptom severity bars (High/Medium/Low).
  * Suggests direct **Treatment Packages** with interactive CTA buttons that let farmers add required bio-fertilizers or protective sprays instantly to their market cart with a single click.

---

### 3. Peer-to-Peer Agricultural Marketplace (`MarketView.tsx` & `Cart.tsx` / `MarketActivity`)
An integrated commerce platform allowing direct supply-chain connections.
* **How It Works:**
  * **Seller Catalog:** Farmers list fresh harvest yields directly to city consumers. Suppliers list certified seed stocks and biological crop protection sprays.
  * **Dynamic Filtering:** Users can filter listings by categories like *Crops*, *Seeds*, *Fertilizers*, and *Tools*, as well as filter by distance using their stored location data.
  * **Transactional Shopping Cart:** Manages in-memory state securely. Adjusts stock availability, aggregates regional delivery surcharges, and provides realistic estimated times of arrival (ETA) for deliveries.

---

### 4. Smart Personalized Dashboard (`Dashboard.tsx` / `HomeActivity`)
A centralized dynamic workspace serving as the farmer’s operational hub.
* **How It Works:**
  * **Simulated Telemetry:** Loads current regional atmospheric data (temperature, soil nitrogen levels, humidity indices) mapped to the selected location.
  * **Seasonal Advisory Banner:** Suggests specific crops to grow dynamically based on the current season and region.
  * **Live Activity Logs:** Stays synchronized with the companion interactive logger, tracking diagnostic counts and transactions.

---

### 5. Government Schemes & Subsidies Database (`SchemesView.tsx` / `SubsidiesActivity`)
A localized database of state-sponsored agricultural incentives.
* **How It Works:**
  * Compiles key active policies (such as crop insurance schemes, organic conversion subsidies, and technical machinery grants).
  * Translates complex legal prerequisites into simple steps, detailing **Eligibility Criteria**, **Required Documents**, and placing clickable launch links directly to official applications.

---

### 6. Interactive Companion Workspaces (`AndroidEmulator.tsx`)
Because FarmWise supports dual deployment, we engineered a dedicated **development companion preview** within the emulator interface.
* **How It Works:**
  * **Logcat Streams:** Real-time log intercepts recording exact actions (`[AUTH] SUCCESS`, `[GEMINI] Processing Image`, `[CART] Item Appended`).
  * **Orientation & System Simulators:** Includes interactive volume toggling, virtual orientation rotations (portrait vs. landscape checks), simulated battery states, and thermal emulation warnings.
  * **APK Compile triggers:** A virtual automation panel highlighting exact native compilation paths (`gradle assembleDebug`) to preview and test the code.

---

## 💻 Tech Stack Specification

### Web Simulator (`/web`)
- **React 19** for modular UI rendering and decoupled layouts.
- **Tailwind CSS** utilizing an premium forest-green palette (`emerald`, `green`, `slate`) suited for agricultural products.
- **Lucide React** for light, high-performance vector icons.
- **TypeScript** declaring strict custom interfaces for all agricultural models.

### Android Native (`/app`)
- **Jetpack Compose** for a modern declarative application canvas.
- **Kotlin Coroutines** for responsive asynchronous operations.
- **CameraX & Coil** handling efficient capture buffers and image compression pipelines.
- **Google GenAI Android SDK** bridging the local mobile instance directly into Gemini networks.

---

## 🔒 Security & Best Practices
1. **Lazy Initialization:** API connectors instantiate dynamic calls strictly during actions rather than module loads, preventing crashes.
2. **Hidden Secrets:** No API keys are embedded within the source files. Standard `.env` environments handle server-side configurations.
3. **Optimized Render Cycles:** Heavy data models have been structurally isolated from general lists inside the components to provide smooth 60fps scrolling both on web and physical screens.
