Silica

    Leaving .NET Behind at the Crime Scene

Silica is a stealth-first, cross-platform tool for programmatically extracting and decrypting sensitive data from web browsers. Built in Rust and C for superior operational security (OPSEC), it is designed from the ground up to evade Endpoint Detection and Response (EDR) systems by leveraging direct syscalls, API hashing, and reflective loading techniques. It targets Chromium-based browsers and Firefox across Windows, macOS, and Linux.

⚠️ Disclaimer: This tool is intended for authorized security assessments, penetration testing, red team engagements, and defensive research only. You must have explicit permission to test any system on which you use this tool. Misuse of this software is strictly prohibited.
Features

    🦀 Native & Stealthy Architecture: Built in Rust and C, compiling to a single native binary to avoid the heavily monitored .NET runtime and associated EDR triggers.

    🔒 Advanced EDR Evasion: Implements direct system calls (syscalls), API hashing, and reflective loading to bypass user-mode hooks and leave minimal forensic traces.

    🌐 Comprehensive Cross-Platform Support:

        Windows: DPAPI-based decryption via stealthy CryptUnprotectData invocation.

        macOS: Keychain integration for master key retrieval.

        Linux: Support for native secret storage (e.g., GNOME Keyring, LibSecret, and raw key file parsing).

    🧩 Multi-Browser Support: Extracts data from a wide range of browsers:

        Chromium-based: Google Chrome, Microsoft Edge, Brave, Vivaldi, Opera.

        Firefox: Full support for its unique NSS-based decryption process.

    💾 Targeted Data Extraction:

        Cookies (for full session hijacking)

        Saved Credentials (plaintext passwords)

        Browser History & Download History

        Local/Session Storage (for JWT tokens and app data)

        Credit Card Information

    📁 Flexible Operational Output: Formats results in JSON, CSV, or Netscape-style cookies.txt for immediate use with tools like Burp Suite, curl, or dedicated browser profiles.

Installation
Prerequisites

    Rust Toolchain: Install via rustup.rs.

    C Build Tools:

        Windows: Visual Studio Build Tools with the "Desktop development with C++" workload.

        macOS: xcode-select --install

        Linux: build-essential (e.g., sudo apt install build-essential)

Building from Source
bash

# 1. Clone the repository
git clone https://github.com/your-username/silica.git
cd silica

# 2. Build the release binary
cargo build --release

# 3. The binary will be located at:
# - Linux/macOS: ./target/release/silica
# - Windows: ./target/release/silica.exe

Usage
Basic Command
bash

silica [OPTIONS]

Examples

Dump all data from all detected browsers to stdout (JSON):
bash

silica

Extract only cookies and credentials, output in Netscape format for Burp Suite:
bash

silica --data cookies,logins --format netscape

Target a specific user profile path (useful for lateral movement):
bash

silica --path "/Users/target_user/Library/Application Support/Google/Chrome/Profile 1"

Save decrypted data to a specific file for later analysis:
bash

silica --output loot.json

Full Options
Flag	Description	Default
-d, --data <TYPES>	Comma-separated data types: cookies, logins, history, downloads, localstorage, cards, all.	all
-f, --format <FORMAT>	Output format: json, csv, netscape.	json
-p, --path <PATH>	Custom path to a browser profile directory to target.	Auto-detected
-o, --output <FILE>	Write output to a file instead of stdout.	
--verbose	Enable verbose output for debugging.	
-h, --help	Print help information.	
-V, --version	Print version information.	
How It Works: Technical Overview

Silica's operation is a multi-stage process designed for stealth and reliability:

    Discovery & Enumeration: Silica scans the system to locate installed browsers and their respective user data directories using known OS-specific paths.

    Master Key Extraction: The core of the tool's stealth. This is handled differently per OS:

        Windows: A custom C module is used to make direct syscalls (via SysWhispers3) to invoke CryptUnprotectData, bypassing hooked Win32 APIs. This decrypts the master key from the Local State file.

        macOS: Interacts with the Apple Keychain via Rust's security-framework crate to securely retrieve the stored encryption key.

        Linux: Parses the key4.db/logins.json (Firefox) or Local State (Chromium) files, often leveraging the system's secret service API or decrypting with a user-provided password.

    Data Decryption: The master key is used to decrypt AES-GCM encrypted data (cookies, passwords) stored within SQLite databases (e.g., Cookies, Login Data).

    Exfiltration & Formatting: The decrypted data is parsed, structured, and output in the operator's chosen format for immediate use.

OPSEC Considerations & Evasion Techniques

Silica is built with the following advanced techniques to avoid detection:

    Direct Syscalls: All Windows API calls related to DPAPI and file operations are performed via direct syscall instructions, completely bypassing EDR user-mode hooks.

    API Hashing: Critical function names (e.g., "CryptUnprotectData") are hashed at compile time and resolved dynamically at runtime to avoid exposing sensitive strings in the binary's Import Address Table (IAT).

    Reflective Loading: The tool can be compiled as a Reflective DLL for in-memory execution within tools like Cobalt Strike, avoiding the need to touch the disk.

    No .NET Runtime: Eliminates the massive OPSEC burden associated with executing .NET assemblies post-exploitation.

Why Rust and C?

The offensive security tooling space is dominated by C# and PowerShell, which are primary targets for EDRs. Silica is built in Rust for its memory safety, zero-cost abstractions, and low-profile OPSEC characteristics. Performance-critical evasion primitives are written in C for maximum control over Windows API calls and raw syscall invocation. This combination provides a powerful, stealthy foundation for modern post-exploitation.
Roadmap

    v0.1 (Current): Core Chromium cookie decryption on Windows with basic syscall evasion.

    v0.2: Add full credential (Login Data) and local storage extraction.

    v0.3: Implement macOS support for Chromium browsers.

    v0.4: Implement Linux support for Chromium browsers.

    v0.5: Add full Firefox support on all three platforms.

    v0.6: Reflective DLL loading support for in-memory execution.

    v1.0: Stable release, comprehensive testing, and performance optimizations.

Contributing

Contributions are what make the open-source community such an amazing place to learn, inspire, and create. Any contributions you make are greatly appreciated.

    Fork the Project

    Create your Feature Branch (git checkout -b feature/AmazingFeature)

    Commit your Changes (git commit -m 'Add some AmazingFeature')

    Push to the Branch (git push origin feature/AmazingFeature)

    Open a Pull Request

Please read CONTRIBUTING.md for detailed guidelines.
License

Distributed under the MIT License. See LICENSE for more information.
Acknowledgments & Inspiration

This project stands on the shoulders of giants and is inspired by the work of:

    Chlonium: The original C# Chromium cookie dumper.

    LaZagne: The legendary credential recovery project.

    Mimikatz/SharpChromium: For pioneering techniques in Windows security and browser forensics.

    The entire open-source security community for its relentless research into EDR evasion.

Contact

Your Name - @YourTwitterHandle - youremail@domain.com

Project Link: https://github.com/your-username/silica

This README provides a strong foundation that is technical, professional, and clearly communicates the ambitious vision of your project. Good luck with the development

