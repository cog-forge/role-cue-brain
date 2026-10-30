---
title: Avatar and Voice Domain
tags:
  - domain
  - 3d
  - avatar
  - avatar-inventory
  - voice-profiles
  - tts
  - visemes
aliases:
  - Avatar Domain
  - 3D Voice Domain
---

# Avatar & Voice Domain

The **Avatar & Voice Domain** governs the visual rendering, blend-shape lip-sync articulation, personalized 3D avatar generation and inventory management, and synthesized speech profiles for virtual technical interview simulations.

---

## 1. Purpose

Provide a lifelike, engaging human presence during virtual technical interviews. Eliminates the impersonality of text bots by delivering real-time 3D facial expressions, accurate speech-synchronized lip movement, customizable voice personas, and personal 3D avatar inventories for Candidates and Recruiters.

---

## 2. Core Concepts

* **3D Virtual Interviewer:**
  A rigged humanoid 3D mesh rendered client-side using WebGL. Supports real-time head movements, idle animations, eye blinks, and facial blend-shape morph targets.
* **Blend-Shape Visemes:**
  Standardized mouth blend-shapes corresponding to phonemic sounds. Morph target weights are interpolated in real time based on timestamped viseme frames delivered with TTS audio.
* **Personal 3D Avatar (`avatars`) & Inventory (`inventories`):**
  An accepted capability delivered through RoleCue's embedded free Avaturn iframe experience. Avaturn handles capture instructions, photo validation, retakes, preview generation, customization, and final GLB generation. RoleCue receives the GLB, converts it to VRM, persists the VRM production asset in `avatars (inventory_id, name, model_path, personality)`, and associates it with the owning user's inventory (`inventories`).
* **Both-Role Avatar Inventory Ownership:**
  Every **Candidate** and **Recruiter** starts with **3 personal avatar storage slots** (`inventories.slot`), ratified on **2026-10-20** (Decision 7). **Administrators do NOT have a personal avatar inventory.**
* **Avatar Storage Capacity (Storage Slots Only):**
  * **Storage Capacity (Avatar Slots):** Avatar slots (`inventories.slot`) represent storage capacity in a user's library; each saved avatar occupies one slot. When inventory capacity is full, avatar creation is refused (`INVENTORY_FULL`) and the user must either remove an existing avatar or purchase additional capacity slots using coins from their wallet, at **1 coin per slot** (1–10 slots per purchase, debited atomically through the shared ledger; Decision 7).
  * **Initial Capacity & API Authority:** Every Candidate and Recruiter starts with 3 storage slots. The frontend reads actual capacity from the authoritative API because purchases can increase it; 3 is the initial allocation, not a permanent frontend limit. Purchased slots are permanent capacity; deleting an avatar frees its slot.
  * **No Generation Fee:** There is **no** separate avatar generation fee (Settled 2026-10-27, Decision 7). Effectively, an avatar beyond the free capacity costs 1 coin (the slot it needs).
* **Free Creation While a Slot Is Available:**
  Opening the avatar creator, Avaturn capture, GLB generation, GLB-to-VRM conversion, and VRM persistence cost nothing as long as the user has a free storage slot. The only avatar charge is buying storage slots.
* **Voice Profile (`voices`):**
  A synthesized vocal persona sourced from third-party Text-to-Speech (TTS) providers. Encapsulates voice name, status (`is_active`), and provider identity (`provider`, `provider_voice_id`, `locale`, `gender`). Curated and managed exclusively by Administrators. Only an active Voice Profile can be selected for a practice session.
  * **Provider Sync (KAN-201, 2026-10-30):** The Admin "Sync from provider" action upserts voices by `(provider, provider_voice_id)`. Voices the provider no longer offers become `is_active = false` and are never deleted, so historical sessions and postings keep their reference. A provider failure or invalid response leaves the catalog unchanged. Because the interview speaks English only, the sync keeps English locales (`en-*`) by default (`TTS_SYNC_LOCALES`).
* **3D Environment Presets:**
  Curated virtual 3D room backgrounds rendered behind the virtual interviewer. Built-in platform presets.
* **2D Waveform Fallback Mode:**
  A performance-resilient fallback interface displaying a responsive audio waveform instead of the 3D WebGL canvas when client hardware lacks GPU acceleration or WebGL support.

---

## 3. Actors Involved

* **Candidate:**
  * Accesses the Personal 3D Avatar Studio in RoleCue; completes capture and customization inside the embedded Avaturn experience.
  * Maintains personal avatar inventory; purchases additional capacity slots using coins when full.
  * Creates avatars free while a storage slot is available; no generation fee is charged.
  * Selects an eligible personal VRM avatar for Target JD practice interviews.
* **Recruiter:**
  * Accesses the Personal 3D Avatar Studio to create, customize, and manage personal avatars in their own avatar inventory.
  * Creates avatars free while a storage slot is available; purchases additional capacity slots using coins from their personal wallet when full.
  * Selects company 3D interviewer models for Job Postings.
* **Administrator:**
  * Curates and manages **Voice Profiles** sourced from external TTS providers (viewing voice profiles, fetching new voice profiles from TTS providers, deleting voice profiles).
  * *Important Invariant:* The Admin does **NOT** manage 3D avatar meshes or 3D background presets (which are built-in presets). Admin has **no** personal avatar inventory.

---

## 4. Main Domain Flow

```mermaid
flowchart TD
    subgraph PersonalAvatar["Personal 3D Avatar Generation & Inventory Flow"]
        P1["User (Candidate or Recruiter) opens Avatar Studio"] --> P2{"Read Actual Capacity from API<br/>(Initially 3 storage slots)"}
        P2 -->|At Capacity| P3["Free storage by removing avatar<br/>OR purchase capacity slot with Coins"]
        P3 --> P2
        P2 -->|Available Capacity| P4["Embedded free Avaturn iframe"]
        P4 --> P5["Avaturn capture instructions and 3 required photos"]
        P5 --> P6["Avaturn validation / retake, preview, and customization"]
        P6 --> P7["Avaturn final GLB generation"]
        P7 --> P8["RoleCue receives final GLB"]
        P8 --> P9{"Free storage slot available?"}
        P9 -->|Has free slot| P10["RoleCue converts GLB to VRM and saves usable VRM asset in User Avatar Inventory<br/>(No charge, uses one free slot)"]
        P9 -->|Inventory full - INVENTORY_FULL| P3
        P10 --> P11["Avatar available in personal library"]
    end

    subgraph RuntimeSync["Runtime Spoken Lip-Sync Flow"]
        R1["Interviewer Spoken Response Text"] --> R2["TTS Provider Synthesis"]
        R2 --> R3["Audio Stream + Viseme Timestamp Data"]
        R3 --> R4["Audio Playback"]
        R3 --> R5["3D Morph Target Viseme Articulation"]
        R4 <--> R5
    end

    subgraph AdminVoice["Admin Voice Profile Governance"]
        V1["Fetch Available Voice Profiles from TTS Provider"] --> V2["Manage / Curate Voice Profiles"]
        V2 --> V3["Available for Session & Posting Configuration"]
    end
```

---

## 5. Business Rules & Invariants

1. **Both-Role Avatar Inventory Ownership:**
   Every Candidate and Recruiter starts with **3 personal avatar storage slots**. Administrators do **not** have an avatar inventory.
2. **Avatar Slots Represent Storage Capacity:**
   The initial 3 slots represent storage capacity; each saved avatar occupies one slot. At capacity, creation is refused (`INVENTORY_FULL`) and the user must remove an existing model or purchase additional capacity slots using wallet coins (1 coin per slot, 1–10 slots per purchase, debited atomically through the shared ledger). Purchased slots are permanent capacity; deleting an avatar frees its slot. The frontend reads actual capacity from the authoritative API.
3. **No Separate Generation Fee:**
   Avatar creation (Avaturn capture, GLB generation, GLB-to-VRM conversion, and VRM persistence) costs nothing as long as the user has a free storage slot (Settled 2026-10-27, Decision 7). The only avatar charge is buying storage slots; effectively, an avatar beyond the free capacity costs 1 coin (the slot it needs).
4. **Embedded Avaturn Free Iframe Boundary:**
   RoleCue embeds the free Avaturn iframe directly. Avaturn owns capture instructions, photo collection, validation and retakes, preview generation, customization, and final GLB generation. RoleCue does not orchestrate these steps through Avaturn Pro APIs or backend reconstruction calls.
5. **VRM is the Production Artifact:**
   RoleCue receives Avaturn's final GLB as an intermediate payload, converts it to standardized VRM, persists the VRM asset, and associates it with the owning user (`user_id`).
6. **No 3D Marketplace:**
   RoleCue does **NOT** provide a 3D asset marketplace, community model trading, or creator monetization. Avatars are restricted to system presets and user-owned personal avatars.
7. **Admin Governance Boundary:**
   * **Admin DOES manage:** Voice Profiles sourced from TTS providers (viewing, fetching, deleting).
   * **Admin does NOT manage:** 3D avatar meshes or 3D background presets.
8. **Speech and Facial Synchronization:**
   TTS audio playback and 3D facial morph animation articulate in tight visual synchronization via phoneme/viseme timing metadata.
9. **Graceful 2D Degradation:**
   If a client browser reports WebGL context loss or insufficient GPU acceleration, the simulation seamlessly switches to 2D Waveform mode without interrupting spoken dialogue.

---

## 6. Relationships to Other Domains

* **[[01_Domains/Interview/README|Interview Domain]]:**
  Supplies visual interviewer meshes and Voice Profiles for live simulation. Candidates may select an eligible personal avatar for Target JD practice; Job Posting interviews use the Recruiter's locked company configuration.
* **[[01_Domains/Payment/README|Payment Domain]]:**
  Debits coins (1 coin per slot) through the shared ledger when purchasing additional avatar storage capacity slots; avatar creation itself is not charged.
* **[[01_Domains/Auth/README|Auth Domain]]:**
  Personal avatars are associated with the authenticated user (`user_id`) for Candidates and Recruiters. Admin accounts do not possess personal avatars.
* **[[01_Domains/Administration/README|Administration Domain]]:**
  Administrators manage the active catalog of Voice Profiles sourced from external TTS providers.

---

## 7. External Integrations

* **TTS Provider:** Synthesizes spoken voice audio and produces phoneme/viseme timing metadata for facial blend-shape animation. **Azure AI Speech** is the selected provider (2026-10-29); see [[02_System/Integrations|Integrations]] §3.3 for the current viseme delivery status.
* **Avaturn:** Provides the embedded free iframe experience for capture, validation, preview, customization, and final GLB generation. RoleCue receives the final GLB and performs GLB-to-VRM conversion and VRM persistence.
