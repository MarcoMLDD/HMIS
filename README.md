# HMIS — Hybrid Memory Integrated Security

A portable, self-contained execution environment for encrypted Python applications. HMIS bundles Python tools into a protected container that decrypts and runs them entirely in memory — leaving no source files on disk.

---

## What it does

HMIS allows you to distribute Python-based tools as a pair of files:

- A small **C launcher** (compiled executable)
- An encrypted **`.hmis` container** (holds the Python source)

When launched, the executable decrypts the container in memory and executes the Python code directly through the Python C API. No temporary files are written. No source code is exposed on disk.

---

## Use cases

| Scenario | Benefit |
| :--- | :--- |
| Distributing internal tools | Source stays encrypted, not readable by end users |
| Protecting configuration logic | Harder to inspect or modify behavior |
| Packaging multi-file Python apps | Single encrypted container instead of many `.py` files |
| Delivery to non-technical users | No Python environment setup required |

---

## Included applications

HMIS currently packages three security-focused tools:

### 1. MIS — File Encryption CLI
A command-line utility for encrypting and decrypting files using AES-256-GCM with password-based key derivation.

```
encrypt → .mis container
decrypt → original file
open    → preview content
info    → show metadata
```

### 2. CMIS — Identity-Aware Encrypted Containers
View-limited, hardware-bound encrypted containers with identity tracking.

- Each user has a machine-bound identity key
- Tracks how many times a file has been opened (global + per user)
- Pre-authorization system for controlled sharing

### 3. MIS Notepad — Encrypted Editor
A graphical notepad and spreadsheet editor with built-in encryption.

- Rich text editing with line numbers, find/replace, undo/redo
- Spreadsheet-style tables (add/delete rows/columns)
- Export to PDF, CSV, or plain text
- All content saved in encrypted `.mis` format

---

## How it works (simplified)

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   hmis      │────▶│  data.hmis  │────▶│  Python     │
│  (runner)   │     │ (encrypted) │     │  execution  │
└─────────────┘     └─────────────┘     └─────────────┘
      │                    │                    │
      │    Contains key    │    Decrypted       │    In-memory
      │    (baked in)      │    at runtime      │    only
```

The runner embeds a decryption key at compile time. At launch, it reads the encrypted `.hmis` container, authenticates and decrypts each Python module, and executes them via the Python C API.

No Python source is ever written to disk — it lives only in RAM during execution.

---

## Requirements (to run)

- No Python installation needed on the target machine
- Compatible with Linux, macOS, and Windows (compiled separately per platform, **ONLY LINUX VERSION IS AVAILABLE NOW**)

---

## Requirements (to build)

If you need to rebuild or repackage the HMIS runner:

- C compiler (gcc / clang / MSVC)
- Python 3.8+ development headers
- OpenSSL 1.1+ (libssl, libcrypto)
- `pip install cryptography` (for the packer script)

---

## Distribution

To distribute a tool built with HMIS, you ship exactly two files:

```
hmis          (or hmis.exe on Windows)
data.hmis
```

The recipient runs the executable. The encrypted payload is loaded and executed transparently.

---

## Limitations

- **Not a compilation** — Python source is encrypted, not compiled to machine code
- **Not reverse-engineering proof** — a skilled attacker with debugging tools can extract the decryption key and recover the source
- **Platform-specific builds** — you must compile separately for each target OS
- **Performance** — adds a small startup overhead for decryption

This is **obfuscation, not encryption** against a determined attacker. It is suitable for protecting casual inspection or distribution of internal tools, not for classified or high-stakes secrets.

---

## License

This tool is distributed as compiled binaries and encrypted payloads. Source code is not provided. See the `LICENSE` file for terms of use.

---

## Credits

Built with:
- OpenSSL — AES-256-GCM encryption
- Python C API — in-memory execution
- cryptography — Python packaging utilities
- customtkinter, reportlab — GUI and PDF export (within encrypted payloads)

---

## Disclaimer

This software is provided "as is", without warranty of any kind. The obfuscation described here is not a substitute for proper security auditing or code protection. Use at your own risk.
```
