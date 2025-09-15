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

**Basic syntax:**
```bash
silica [OPTIONS]
Examples
```
**Dump all data from all detected browsers to the terminal (JSON):**
```bash

silica
```

**Dump only cookies and output in a Netscape format for Burp Suite:**
```bash

silica --data cookies --format netscape
```
**Target a specific browser profile path:**
```bash

silica --path "/Users/example/Library/Application Support/Google/Chrome/Default"
```
**Save output to a file:**
```bash

silica --output loot.json

Options

    --data <TYPES> Comma-separated list of data to dump: cookies, logins, history, downloads, localstorage, all (default: all)

    --format <FORMAT> Output format: json, csv, netscape (default: json)

    --path <PATH> Specify a custom browser profile path to target.

    --output <FILE> Write output to a file instead of stdout.

    -h, --help Print help information.
```
## How It Works

**Silica's operation is broken down into several stages:**

    Discovery: Identifies installed browsers and their user data directories on the target system.

    Key Extraction: The core stealth component. Uses OS-specific methods to retrieve the master encryption key:

        Windows: Leverages a custom C module using direct syscalls to invoke CryptUnprotectData, bypassing hooked WinAPI functions.

        macOS: Interacts with the Apple Keychain via Rust's security-framework crate.

        Linux: Parses stored key files or interacts with the native secret service (e.g., GNOME Keyring via libsecret).

    Decryption: Uses the extracted key to decrypt AES-GCM encrypted data (cookies, passwords) from the browser's SQLite databases.

    Exfiltration: Formats the decrypted data for easy use by the operator.

## Why Rust and C?

The offensive security space is dominated by C# and PowerShell, which are heavily monitored by EDRs. Silica is built in Rust for its memory safety, performance, and low-profile OPSEC characteristics. Critical evasion primitives are written in C for maximum control over Windows API calls and syscall invocation. This combination provides a powerful, stealthy foundation for modern post-exploitation tools.
Roadmap

       Full Chromium support (Windows/macOS/Linux)

       Full Firefox support (Windows/macOS/Linux)

       Direct Syscall implementation (C module)

       Reflective loading support

       JSON, CSV, Netscape output formats

       Unit and Integration Testing

## Contributing

** Contributions, ideas, and bug reports are welcome! Feel free to open an issue or a pull request.
Please read the CONTRIBUTING.md file for guidelines.
License

This project is licensed under the MIT License - see the LICENSE file for details.


## Acknowledgments

    Inspired by the techniques of tools like Chlonium, LaZagne, Mimikatz, and SharpChromium.

    Thanks to the open-source security community for its relentless research into EDR evasion and browser forensics.
