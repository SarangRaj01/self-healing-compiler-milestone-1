<div align="center">

# 🛡️ Self-Healing Compiler (SHC) — Milestone 1 (25% Prototype)
### *Next-Gen Runtime Memory Bounds Protection & Zero-Downtime Resilience*

[![Language](https://img.shields.io/badge/Language-C11%20%7C%20Python%203.14-00599C?style=for-the-badge&logo=c&logoColor=white)](https://en.wikipedia.org/wiki/C11_(C_revision))
[![Compiler](https://img.shields.io/badge/Toolchain-MinGW%20GCC%20%2F%20Clang-black?style=for-the-badge&logo=gnu&logoColor=white)](https://gcc.gnu.org/)
[![Status](https://img.shields.io/badge/Milestone-25%25%20Verified%20Passed-brightgreen?style=for-the-badge&logo=checkmarx&logoColor=white)](docs/DEMO_GUIDE.md)
[![Service Uptime](https://img.shields.io/badge/Process%20Survival-100%25%20Zero%20Crash-blueviolet?style=for-the-badge)](docs/PRESENTATION_25_PERCENT.md)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

<br/>

**BCSE307P — Compiler Design Lab Capstone (First 25% Deliverable)**  
*Vellore Institute of Technology (VIT)*

<br/>

<!-- ==================== COMICAL STORY ANIMATION ==================== -->
<p align="center">
  <img src="assets/comic_story.svg" alt="Comic Story: Traditional Compiler vs Self-Healing Compiler" width="100%" />
</p>

<!-- ==================== ANIMATED LIVE TERMINAL DEMO ==================== -->
<p align="center">
  <img src="assets/terminal_demo.svg" alt="SHC Live Execution Terminal Demo" width="100%" />
</p>

</div>

---

## 🎭 The Story: What is Actually Happening in This Project?

Imagine software security like an **apartment building with a guard**:

```
🏢 THE TRADITIONAL COMPILER (GCC / Clang):
   1. The architect builds a door with a lock (Stack Canary / ASLR).
   2. The architect immediately LEAVES the building forever.
   3. A burglar shows up with a battering ram (70-byte attack into a 32-byte buffer).
   4. The building's emergency system panics and DETONATES DYNAMITE (abort() / SIGABRT).
   5. The entire building collapses! Nobody else can enter. (Denial of Service).
   6. You have to call construction workers to rebuild it from scratch (30-minute offline rebuild).

🛡️ THE SELF-HEALING COMPILER (SHC):
   1. The compiler hires a PERMANENT, SMART IN-PROCESS BODYGUARD (Runtime Monitor).
   2. The compiler intercepts all doors: transforms dangerous strcpy() into checked shc_safe_strcpy().
   3. The burglar arrives with the 70-byte payload.
   4. The bodyguard measures the intruder at the doorstep:
      "Wait! You are 70 bytes, but this room only holds 32 bytes!"
   5. The bodyguard DENIES ENTRY BEFORE THEY STEP FOOT INSIDE.
   6. Zero damage. Zero memory corruption.
   7. The building STAYS OPEN (Exit Code: 0) and welcomes the next legitimate guest! 🚀
```

---

## ⚡ What is Implemented in This 25% Milestone?

This folder is the standalone, demonstrable foundation for the **first 25% review**:

1. **Autonomous Compiler Driver (`compiler/shc_cc.py`):**
   - Automatically detects your system C compiler (`gcc` or `clang`).
   - Compiles C code with `-O2 -Iinclude`, links the runtime library, and verifies binary generation.
   - Accurately captures syntax errors and returns non-zero exit codes.
2. **C Source Instrumentation Pass (`compiler/shc_instrument.py`):**
   - Scans C source code for fixed-size buffer allocations (`char buf[N]`).
   - Automatically rewrites unsafe calls:
     ```c
     // BEFORE: Unsafe raw copy
     strcpy(dest_buf, user_input);

     // AFTER: Auto-instrumented bounds-checked call
     shc_safe_strcpy(dest_buf, sizeof(dest_buf), user_input);
     ```
3. **Runtime Security Checker (`runtime/shc_runtime.c`):**
   - Inspects `src_len + 1 > dst_capacity` **before copying**.
   - Rejects the operation cleanly if oversized, returning `SHC_ERR_OVERFLOW` with detailed diagnostics.
   - Maintains real-time telemetry counters for passed checks, blocked attacks, and logged anomalies.
4. **Faculty Demonstration Benchmark (`benchmarks/benchmark_bounds.c`):**
   - Executes a live 3-stage sequence:
     - **Test 1:** Valid input copied safely.
     - **Test 2:** Oversized input intercepted & blocked without memory corruption.
     - **Test 3:** Subsequent valid request processed with **100% uptime**.
5. **Automated Verification Suite (`tests/test_bounds_demo.py`):**
   - Pre-flight automated test harness verifying that all components pass 100%.

---

## 👥 Research Team & Contributions

| Register No | Student Name | Role & Responsibility |
| :---: | :---: | :--- |
| **24BDS0091** | **Sarang Raj** | Problem Identification, Milestone Benchmark Design, Literature Survey |
| **24BCE0920** | **Antony Roy** | Compiler Driver Architecture, Novelty Formulation, CLI Pass |
| **24BDS0080** | **Dev Raj** | Runtime Bounds Engine, Presentation Deck, Report Compilation |

---

## 🚀 Live Demonstration (Single Command)

Open PowerShell inside this directory:
```powershell
cd "C:\Users\SARANG RAJ\.gemini\antigravity\scratch\self_healing_compiler_25_percent"
```

### 1. Run the Live Faculty Demo
```powershell
python compiler/shc_cc.py benchmarks/benchmark_bounds.c -o build/demo.exe --run
```

### 2. Run Automated Test Verification
```powershell
python tests/test_bounds_demo.py
```

### 3. Test Automatic Source-to-Source Instrumentation
```powershell
python compiler/shc_cc.py benchmarks/sample_uninstrumented.c -o build/auto.exe --instrument --run
```

---

## 📊 Live Demonstration Output

```text
================================================================================
   SELF-HEALING COMPILER (SHC) - MILESTONE 1 RUNTIME BOUNDS CHECK DEMO          
   BCSE307P: Compiler Design Lab (25% Deliverable)                             
================================================================================

[SHC Runtime] Initialized Self-Healing Compiler Runtime v1.0.0
[SHC Runtime] Active protections: Shadow-Stack, Dynamic-CFI, Bounds-Verification, In-Place Hot-Patching
>>> TEST 1: Valid Input Processing <<<
  Destination buffer capacity: 32 bytes
  Input data:                 "Hello, SHC!" (length: 11 bytes)
  Result: [SUCCESS] Input safely stored in buffer: "Hello, SHC!"

>>> TEST 2: Oversized Input Detection & Rejection <<<
  Destination buffer capacity: 32 bytes
  Attempting to copy:         "ATTACK_PAYLOAD_EXCEEDING_THE_D..." (length: 70 bytes)
[SHC RUNTIME DETECTION] Memory Bounds Violation Detected!
  Reason: Source length (70 + 1 null byte = 71 bytes) exceeds destination buffer capacity (32 bytes).
  Action: Operation rejected before copy. Memory preserved without out-of-bounds write.
  Result: [DETECTED & REJECTED] Malicious/oversized copy rejected before memory corruption!
  Buffer preservation check:  Buffer content remains untouched: "Hello, SHC!"

>>> TEST 3: Continued Execution & Service Continuity <<<
  Destination buffer capacity: 32 bytes
  Input data:                 "Safe Post-Attack Request" (length: 24 bytes)
  Result: [SUCCESS] System is healthy and accepted valid request: "Safe Post-Attack Request"

================================================================================
                           DEMONSTRATION AUDIT SUMMARY                          
================================================================================
Total memory checks performed: 3
Successful checks (Passed):   2
Rejected operations (Blocked): 1
Anomalies logged in runtime:  1
Demonstration Status:         ALL 3 TESTS PASSED SUCCESSFULLY
Process Status:               EXIT CODE 0 (NO CRASH, NO MEMORY CORRUPTION)
================================================================================
```

---

## 📚 Documentation & Faculty Review Links

- 📖 **[`docs/DEMO_GUIDE.md`](docs/DEMO_GUIDE.md)**: Line-by-line breakdown of output, explanation of each status code, and faculty Q&A points.
- 🎓 **[`docs/PRESENTATION_25_PERCENT.md`](docs/PRESENTATION_25_PERCENT.md)**: Complete slide-by-slide script for explaining the 25% review in front of faculty.

---

## 🗺️ Project Roadmap (What's Next?)

```
[====== Milestone 1 (25%) ======]  --> COMPLETED ✅ (Compiler Driver, Source Pass, Runtime Bounds)
[------ Milestone 2 (50%) ------]  --> UPCOMING ⏳ (Function Shadow Stack & Return Address Tracking)
[------ Milestone 3 (75%) ------]  --> UPCOMING ⏳ (Forward Control-Flow Integrity & Frame Re-Randomization)
[------ Milestone 4 (100%) -----]  --> UPCOMING ⏳ (In-Place Trampoline Hot-Patching & Full Benchmarking)
```

---

## 📜 License
Developed for **BCSE307P: Compiler Design Lab** at **Vellore Institute of Technology (VIT)**.  
Licensed under the [MIT License](LICENSE).
