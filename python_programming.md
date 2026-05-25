# Programming for Everybody

> **Core Idea:** Computers are remarkably good at text analysis and repetitive tasks — but they only do exactly what they are told.

---

## How a Computer Works — Architecture Overview

```mermaid
flowchart TD
    IO["🖱️ INPUT / OUTPUT DEVICES\nMouse · Keyboard · Printers · Screen"]

    subgraph SYS["  COMPUTER SYSTEM  "]
        direction TB
        SW["📦 SOFTWARE\n(Programs & Operating System)"]

        subgraph PROC["  PROCESSOR LAYER  "]
            CPU["⚡ CENTRAL PROCESSING UNIT\n3 billion operations / second"]
        end

        MM["🟠 MAIN MEMORY  —  RAM\nFast · Small · Temporary\n(lost on reboot)"]
    end

    SM["💾 SECONDARY MEMORY\nDisk Drives · USB Sticks\nSlow · Large · Permanent\n(stays until deleted)"]

    IO     <-->|"double arrow — user interaction"| SW
    SW      -->|"sends instructions"| CPU
    CPU    <-->|"reads & writes"| MM
    SW     <-->|"reads & writes"| SM

    style IO   fill:#43A047,color:#fff,stroke:#2E7D32,stroke-width:2px
    style SW   fill:#1E88E5,color:#fff,stroke:#1565C0,stroke-width:2px
    style CPU  fill:#E53935,color:#fff,stroke:#B71C1C,stroke-width:2px
    style MM   fill:#FB8C00,color:#fff,stroke:#E65100,stroke-width:2px
    style SM   fill:#8E24AA,color:#fff,stroke:#4A148C,stroke-width:2px
    style SYS  fill:#E3F2FD,stroke:#90CAF9,stroke-width:2px
    style PROC fill:#FCE4EC,stroke:#EF9A9A,stroke-width:2px
```

---

## The 4 Core Hardware Components

### 1. Input / Output Devices
> The interface between the human and the machine.

| Device Type | Examples |
|-------------|---------|
| Input | Mouse, Keyboard, Microphone, Scanner |
| Output | Monitor, Printer, Speakers |
| Both | Touchscreen, USB Drive |

---

### 2. Central Processing Unit (CPU)

```mermaid
flowchart LR
    A["📥 Fetch\nNext Instruction"] --> B["🔍 Decode\nWhat does it mean?"]
    B --> C["⚙️ Execute\nDo the operation"]
    C --> D["📤 Store\nWrite result"]
    D --> A

    style A fill:#E53935,color:#fff,stroke:#B71C1C
    style B fill:#F4511E,color:#fff,stroke:#BF360C
    style C fill:#FB8C00,color:#fff,stroke:#E65100
    style D fill:#FDD835,color:#212121,stroke:#F9A825
```

**Key facts:**
- Contains **millions of transistors** — tiny switches that store 0s and 1s
- Runs at **~3 billion cycles per second** (3 GHz)
- The CPU is **not the "brain"** — it is incredibly fast but completely dumb
- It only ever asks one question: **"What do I do next?"**
- The *software* provides all the intelligence — the CPU just executes blindly

---

### 3. Main Memory (RAM)

```mermaid
flowchart LR
    subgraph RAM["  🟠 MAIN MEMORY — RAM  "]
        direction LR
        R1["Address 0\n01001000"]
        R2["Address 1\n11010011"]
        R3["Address 2\n00110101"]
        R4["Address …\n…"]
    end

    CPU2["⚡ CPU"] <-->|"nanoseconds"| RAM

    style RAM  fill:#FFF3E0,stroke:#FB8C00,stroke-width:2px
    style CPU2 fill:#E53935,color:#fff,stroke:#B71C1C
    style R1   fill:#FFE0B2,stroke:#FB8C00
    style R2   fill:#FFE0B2,stroke:#FB8C00
    style R3   fill:#FFE0B2,stroke:#FB8C00
    style R4   fill:#FFE0B2,stroke:#FB8C00
```

| Property | Value |
|----------|-------|
| Speed | Very fast (nanoseconds) |
| Capacity | Small (GB range) |
| Persistence | **Temporary** — wiped on reboot |
| Purpose | Holds the currently running program & data |

> **RAM = working desk.** Everything you're actively working on sits here. Turn off the power → desk is cleared.

---

### 4. Secondary Memory

```mermaid
flowchart LR
    SW2["📦 Software"] <-->|"milliseconds\n(much slower than RAM)"| SM2

    subgraph SM2["  💾 SECONDARY MEMORY  "]
        direction TB
        D1["🖴 Hard Disk Drive\n(magnetic platters)"]
        D2["💿 SSD\n(flash chips)"]
        D3["🔌 USB Memory Stick\n(flash chips)"]
    end

    style SW2 fill:#1E88E5,color:#fff,stroke:#1565C0
    style SM2 fill:#EDE7F6,stroke:#8E24AA,stroke-width:2px
    style D1  fill:#CE93D8,stroke:#7B1FA2
    style D2  fill:#CE93D8,stroke:#7B1FA2
    style D3  fill:#CE93D8,stroke:#7B1FA2
```

| Property | Value |
|----------|-------|
| Speed | Slower (milliseconds) |
| Capacity | Large (TB range) |
| Persistence | **Permanent** — survives reboots |
| Purpose | Long-term storage of files, programs, databases |

> **Secondary memory = filing cabinet.** Data stays here until you deliberately delete it.

---

## Memory Comparison at a Glance

```mermaid
quadrantChart
    title Speed vs Capacity — Memory Types
    x-axis Low Capacity --> High Capacity
    y-axis Slow --> Fast
    quadrant-1 Fast & Large
    quadrant-2 Fast & Small
    quadrant-3 Slow & Small
    quadrant-4 Slow & Large
    CPU Registers: [0.05, 0.98]
    RAM: [0.25, 0.80]
    SSD: [0.65, 0.40]
    Hard Disk Drive: [0.85, 0.15]
    USB Stick: [0.70, 0.10]
```

| | RAM | Secondary Memory |
|--|-----|-----------------|
| **Speed** | ⚡ Very Fast | 🐢 Much Slower |
| **Size** | Small (GBs) | Large (TBs) |
| **Cost / GB** | Expensive | Cheap |
| **Survives reboot?** | ❌ No | ✅ Yes |
| **Connected to** | CPU directly | Motherboard |

---

## What Holds It All Together

```mermaid
flowchart TD
    MB["🔌 MOTHERBOARD\nPhysical circuit board that\nconnects every component"]

    MB --> CPU3["⚡ CPU"]
    MB --> RAM3["🟠 RAM"]
    MB --> SM3["💾 Secondary Storage\n(via SATA / NVMe / USB)"]
    MB --> IO3["🖱️ I/O Ports\n(USB, HDMI, Audio)"]

    subgraph TS["  HOW DATA IS STORED INSIDE  "]
        T1["Transistor OFF = 0"]
        T2["Transistor ON  = 1"]
        T3["Millions of transistors\n= billions of 0s and 1s\n= all data & programs"]
    end

    MB --> TS

    style MB  fill:#00897B,color:#fff,stroke:#004D40,stroke-width:2px
    style CPU3 fill:#E53935,color:#fff,stroke:#B71C1C
    style RAM3 fill:#FB8C00,color:#fff,stroke:#E65100
    style SM3  fill:#8E24AA,color:#fff,stroke:#4A148C
    style IO3  fill:#43A047,color:#fff,stroke:#2E7D32
    style TS   fill:#E0F7FA,stroke:#00ACC1,stroke-width:2px
    style T1   fill:#B2EBF2,stroke:#00ACC1
    style T2   fill:#B2EBF2,stroke:#00ACC1
    style T3   fill:#B2EBF2,stroke:#00ACC1
```

---

## The Golden Rule of Computing

```
  ┌───────────────────────────────────────────────────────────────┐
  │                                                               │
  │   Software  =  Intelligence        (written by humans)       │
  │   CPU       =  Raw Speed           (dumb but blazing fast)   │
  │   RAM       =  Working Space       (fast, temporary)         │
  │   Disk      =  Long-term Storage   (slow, permanent)         │
  │                                                               │
  │   The CPU is NOT the brain. It blindly runs whatever         │
  │   instruction software gives it — 3 billion times/sec.       │
  │                                                               │
  └───────────────────────────────────────────────────────────────┘
```

---

## Key Vocabulary

| Term | Definition |
|------|-----------|
| **Transistor** | Tiny electronic switch (ON=1, OFF=0); millions packed into CPU & memory chips |
| **RAM** | Random Access Memory — fast, temporary workspace for running programs |
| **Motherboard** | Circuit board that physically connects CPU, RAM, storage, and I/O |
| **Magnetic Storage** | HDDs store data using magnetized particles on spinning platters |
| **Secondary Storage** | Any persistent storage: HDD, SSD, USB — connects via motherboard |
| **GHz** | Gigahertz — billions of cycles per second; measures CPU clock speed |
