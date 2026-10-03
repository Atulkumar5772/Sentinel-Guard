<div align="center">

<!-- 🌟 HEADER BANNER -->
<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=1,6,24&height=220&section=header&text=Sentinel-Guard&fontSize=48&fontColor=ffffff&animation=twinkling&desc=Linux%20Kernel%20File%20Integrity%20Monitoring%20and%20Anti-Tamper%20Security%20Daemon&descSize=18&descAlignY=70&descAlign=50" width="100%"/>

<p align="center">
  <img src="https://img.shields.io/badge/Language-C%2B%2B17-00599C?style=for-the-badge&logo=c%2B%2B&logoColor=white" />
  <img src="https://img.shields.io/badge/Platform-Red_Hat_Linux-EE0000?style=for-the-badge&logo=redhat&logoColor=white" />
  <img src="https://img.shields.io/badge/Kernel_Subsystem-inotify-FCC624?style=for-the-badge&logo=linux&logoColor=black" />
  <img src="https://img.shields.io/badge/Cryptography-OpenSSL_SHA--256-38BDF8?style=for-the-badge&logo=openssl&logoColor=white" />
  <img src="https://img.shields.io/badge/License-All_Rights_Reserved-red?style=for-the-badge" />
</p>

<p align="center">
  <b>Sentinel-Guard</b> is a high-performance, asynchronous Linux system daemon engineered in <b>C++17</b>. It interfaces directly with the <b>Linux Kernel <code>inotify</code> subsystem</b> to deliver real-time File Integrity Monitoring (FIM), instantaneous unauthorized alteration detection via cryptographic SHA-256 baseline auditing, and automated tamper alert pipelines.
</p>

</div>

---

## ⚡ Terminal Live Execution Preview

```
========================================================================
   ____             __  _            __     ______                     __
  / __/__ ___  ___ / /_(_)__  ___   / /    / ____/_ _____ _________  _/ /
 _\ \/ -_) _ \/ -_) __/ / _ \/ -_) / /__  / (_ / // / _ `/ __/ _  / / _ /
/___/\__/_//_/\__/\__/_/_//_/\__/ /____/  \___/\_,_/\_,_/_/  \_,_/ /_//_/ 
========================================================================
[19:10:14] [INFO]     Initializing Sentinel-Guard Daemon v1.0.0...
[19:10:14] [INFO]     Target directory to protect: /etc/security/critical_configs
[19:10:14] [SUCCESS]  Generating SHA-256 Cryptographic Baseline snapshot...
[19:10:14] [SUCCESS]  Attached Linux Kernel inotify watcher [FD: 4, Watch Descriptor: 1]
[19:10:14] [INFO]     Daemon Active. Listening for kernel filesystem events...
------------------------------------------------------------------------
[19:10:22] [CRITICAL] Kernel Event [IN_MODIFY] on: /etc/security/critical_configs/secret.txt
[19:10:22] [CRITICAL] 🚨 SHA-256 HASH MISMATCH DETECTED!
                      Baseline : e3b0c44298fc1c149afbf4c8996fb92427ae41e4...
                      Current  : 8f434346648f6b96df89dda901c5176b10a6d839...
[19:10:22] [WARN]     Kernel Event [IN_ATTRIB] ⚠️  Permissions altered (0644 -> 0777)
[19:10:25] [CRITICAL] Kernel Event [IN_DELETE] 🚨 File removed: /etc/security/critical_configs/config.sys
```

---

## 🏗️ System Architecture & Subsystems

```mermaid
flowchart TD
    subgraph Userspace ["🛡️ Userspace Application (Sentinel-Guard)"]
        direction TB
        Main["Main Daemon Controller\n(Signal Handler & CLI Loop)"]
        
        subgraph CoreEngine ["Core Security Subsystems"]
            Watcher["Kernel Watcher Engine\n(Event Buffer Dispatcher)"]
            Verifier["Integrity Verification Engine\n(OpenSSL SHA-256 + State Auditor)"]
            Logger["ANSI Audit Logger\n(Structured Diagnostics)"]
        end

        Main --> Watcher
        Main --> Verifier
        Main --> Logger
    end

    subgraph Kernel ["🐧 Linux Kernel Space"]
        VFS["Virtual Filesystem (VFS)"]
        InotifySubsys["inotify Subsystem\n(sys/inotify.h)"]
        EventQueue[("Kernel Event Queue\n(IN_MODIFY / IN_DELETE / IN_ATTRIB)")]
        
        VFS --> InotifySubsys
        InotifySubsys --> EventQueue
    end

    subgraph Storage ["💾 Filesystem & Baseline Store"]
        TargetDir[("Monitored Sensitive Directory\n(System Assets & Configs)")]
        BaselineStore[("Cryptographic Baseline Store\n(Secure Encrypted State)")]
    end

    TargetDir -.->|Filesystem I/O Activity| VFS
    EventQueue ==>|Asynchronous Event FD| Watcher
    Watcher -->|Trigger Event Callback| Verifier
    Verifier <-->|Verify Hash & Metadata| TargetDir
    Verifier <-->|Compare with Baseline Snapshot| BaselineStore
    Verifier -->|Dispatch Status & Alerts| Logger
```

---

## 🔄 Real-Time Threat Interception Lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Attacker as 👤 Attacker / Malicious Process
    participant FS as 📁 Protected Filesystem
    participant Kernel as 🐧 Linux Kernel (inotify)
    participant Sentinel as 🛡️ Sentinel Daemon
    participant Verifier as 🔐 Verification Engine
    participant Log as 📊 Audit Log

    Sentinel->>Verifier: Initialize Baseline Snapshot (SHA-256 + Metadata)
    Verifier->>FS: Scan Files & Compute Cryptographic Hashes
    Sentinel->>Kernel: inotify_add_watch(TargetDir, IN_MODIFY | IN_DELETE | IN_ATTRIB)

    Note over Sentinel,Kernel: Daemon enters non-blocking async event loop

    Attacker->>FS: Injects malicious bytes into sensitive files
    FS->>Kernel: VFS File Modification Triggered
    Kernel->>Sentinel: inotify_event (IN_MODIFY)
    Sentinel->>Log: [CRITICAL] Kernel Event IN_MODIFY Detected
    Sentinel->>Verifier: verifyIntegrity("sensitive_file")
    Verifier->>FS: Re-compute Live SHA-256 Hash
    Verifier-->>Sentinel: Hash Mismatch == TRUE (Tampering Confirmed)
    Sentinel->>Log: 🚨 Alert: Cryptographic Hash Mismatch & Metadata Altered
```

---

## 🌟 Core Engineering Capabilities

<div align="center">

| Capability | Technical Implementation | Benefit |
| :--- | :--- | :--- |
| **Zero-Polling Watcher** | Direct Linux Kernel `inotify` API | Zero CPU spinning; purely event-driven architecture |
| **Cryptographic Auditing** | OpenSSL EVP SHA-256 engine | Detects single-bit unauthorized content tampering |
| **Deep Metadata Tracking** | POSIX `stat()` tracking (`mode_t`, `uid`, `gid`) | Identifies permission tampering (`chmod 777`) and ownership changes |
| **Multi-Event Interception** | `IN_MODIFY`, `IN_DELETE`, `IN_ATTRIB`, `IN_CREATE` | Full coverage against file deletion, injection, and corruption |
| **Graceful POSIX Shutdown** | `SIGINT` (Ctrl+C) and `SIGTERM` atomic flags | Prevents resource leaks and cleans up kernel file descriptors |
| **Automated Baseline Store** | Encrypted snapshot state | Retains ground-truth file states for security auditing |

</div>

---

## 🛠️ Technology Stack & Dependencies

```
Languages & Standards : C++ (C++17 Standard), POSIX.1-2008
Kernel Subsystems     : Linux Kernel inotify (sys/inotify.h)
Cryptography          : OpenSSL (libcrypto / EVP Message Digest)
Operating System      : Red Hat Enterprise Linux (RHEL 9) / Fedora / Linux 6.x
Build Tools           : GNU Make, GCC / G++ (v11+)
```

---

## 💻 CLI Options & Usage

```bash
# Display help and CLI options
$ ./bin/sentinel-guard --help

# Monitor default directory
$ ./bin/sentinel-guard

# Monitor a specific sensitive system directory
$ ./bin/sentinel-guard -d /etc/security/critical_configs

# Print version and build specs
$ ./bin/sentinel-guard --version
```

### ⚙️ Command Line Flags:
* `-d, --dir <path>` : Specify target directory path to protect and monitor.
* `-h, --help`       : Display interactive CLI manual and banner.
* `-v, --version`    : Display version and kernel subsystem information.

---

## 🔒 Confidentiality & Intellectual Property Notice

> [!WARNING]
> **Enterprise Internship & Academic Development Project • All Rights Reserved**  
> The core algorithmic implementations, inotify event loop dispatcher, and baseline verification logic are the intellectual property of **Atul Kumar**. 
> 
> Unauthorized reproduction, cloning, or distribution of this software or its internal architecture is strictly prohibited under applicable copyright laws.
> 
> *Authorized demonstration and full codebase inspection are available for recruiters and faculty upon formal request.*

---

<div align="center">
  <sub>Developed & Engineered by <b>Atul Kumar</b> • Red Hat Intern</sub>
</div>
