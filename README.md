# Silica

> Leaving .NET Behind at the Crime Scene

**Silica** is a stealth-focused, cross-platform tool for extracting and decrypting browser data (cookies, credentials, history) from Chromium-based browsers and Firefox. Built in Rust and C for superior OPSEC, it implements direct syscalls and API hashing to evade EDR hooks and leave minimal traces.

**Disclaimer: This tool is intended for authorized security assessments, red team engagements, and educational purposes only. Never use it on systems you do not own or have explicit permission to test.**

## Features

*   **🦀 Native & Stealthy:** Built in Rust and C, compiling to a single native binary to avoid the suspicious .NET runtime and associated EDR triggers.
*   **🔒 Advanced Evasion:** Implements direct system calls (syscalls) and API hashing to bypass user-mode EDR hooks on critical functions.
*   **🌐 Cross-Platform:** Operates on Windows, macOS, and Linux.
*   **🧩 Browser Support:** Extracts data from:
    *   Chromium-based browsers (Chrome, Edge, Brave, Vivaldi)
    *   Firefox
*   **💾 Data Targeting:** Retrieves:
    *   Cookies (with full decryption for session hijacking)
    *   Saved Credentials
    *   Browser History
    *   Download History
    *   Local Storage (for JWT tokens and other data)
*   **📁 Flexible Output:** Formats results in JSON, CSV, or Netscape-style `cookies.txt` for immediate use with tools like Burp Suite or `curl`.

## Installation

1.  **Prerequisites:** Ensure you have Rust and a C toolchain installed.
    ```bash
    # Rust (via rustup)
    https://rustup.rs/

    # C Toolchain (Windows: Visual Studio Build Tools, macOS: Xcode CLI Tools, Linux: build-essential)
    ```

2.  **Build from Source:**
    ```bash
    git clone https://github.com/your-username/silica.git
    cd silica
    cargo build --release
    ```
    The compiled binary will be located at `./target/release/silica` (or `.exe` on Windows).

## Usage

Basic syntax:
```bash
silica [OPTIONS]
