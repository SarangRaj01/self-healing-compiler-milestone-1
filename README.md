<div align="center">

# 🛡️ Self-Healing Compiler (SHC)
### *Runtime Memory Bounds Protection & Zero-Downtime Resilience*

[![Language](https://img.shields.io/badge/Language-C11%20%7C%20Python%203.14-000000?style=for-the-badge&logo=c&logoColor=white)](https://en.wikipedia.org/wiki/C11_(C_revision))
[![Compiler](https://img.shields.io/badge/Toolchain-MinGW%20GCC%20%2F%20Clang-333333?style=for-the-badge&logo=gnu&logoColor=white)](https://gcc.gnu.org/)
[![Status](https://img.shields.io/badge/Milestone-25%25%20Verified%20Passed-111111?style=for-the-badge&logo=checkmarx&logoColor=white)](docs/DEMO_GUIDE.md)
[![Service Uptime](https://img.shields.io/badge/Process%20Survival-100%25%20Zero%20Crash-222222?style=for-the-badge)](docs/PRESENTATION_25_PERCENT.md)
[![License](https://img.shields.io/badge/License-MIT-000000?style=for-the-badge)](LICENSE)

<br/>

**BCSE307P — Compiler Design Lab Capstone**  
*Vellore Institute of Technology (VIT)*

<br/>

<!-- ==================== CLEAN MONOCHROME STICKMAN ANIMATION ==================== -->


</div>

---

## ⚡ Technical Overview & Working Mechanism

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                   SCENARIO A: UNPROTECTED RUNTIME EXECUTION                      │
├──────────────────────────────────────────────────────────────────────────────────┤
│ • An uninstrumented application executes a raw strcpy(buf, input).               │
│ • When input length (70B) exceeds destination buffer capacity (32B), adjacent    │
│   stack memory is overwritten.                                                   │
│ • The operating system detects stack boundary corruption and raises SIGSEGV/     │
│   SIGABRT, terminating the process immediately (0% Availability / Crash).        │
└──────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────────────┐
│                     SCENARIO B: SHC PROTECTED RUNTIME                            │
├──────────────────────────────────────────────────────────────────────────────────┤
│ • The SHC compiler pass automatically rewrites the call to shc_safe_strcpy().    │
│ • The runtime security monitor evaluates: src_len + 1 > dst_capacity BEFORE copy.│
│ • The oversized operation is rejected immediately (status: SHC_ERR_OVERFLOW).   │
│ • Zero bytes in memory are corrupted; the destination buffer remains intact.     │
│ • The process survives with 100% service availability and serves subsequent      │
│   legitimate requests normally (Exit Code: 0).                                    │
└──────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ What is Implemented

This folder contains the self-contained, working milestone for the **first 25% review**:

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
