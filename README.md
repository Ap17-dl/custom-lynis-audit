# Lynis macOS Security Enhancements

Custom security auditing tests developed using **Lynis** to assess macOS security configurations, focusing on FileVault disk encryption and the built-in Application Firewall.

**Author:** Ankush Pratham  
**Platform:** macOS  
**Framework:** Lynis  
**Language:** Shell scripting

## Overview

Lynis is an open-source security auditing tool that evaluates system configurations, identifies potential security weaknesses, and provides recommendations for improving system hardening.

This project extends Lynis with two custom security checks:

- **CRYP-9001 — FileVault Encryption Audit:** Checks whether macOS FileVault disk encryption is enabled.
- **FIRE-9001 — macOS Firewall Audit:** Checks the macOS Application Firewall status and stealth-mode configuration.

These enhancements demonstrate how Lynis's custom-test mechanism can be used to audit platform-specific security features.

## Project Structure

```text
lynis-macos-security-enhancements/
├── README.md
├── tests_custom
├── tests_firewall_macos
```

- `tests_custom` contains the custom tests loaded by Lynis, including the FileVault and firewall audit logic.
- `tests_firewall_macos` contains the standalone macOS firewall audit script.

## Enhancement 1: FileVault Encryption Audit

**Test ID:** `CRYP-9001`

FileVault provides disk encryption for supported macOS systems. This test uses the `fdesetup status` command to inspect the reported encryption status.

### Checks performed

- Determines whether the `fdesetup` utility is available.
- Retrieves the FileVault status.
- Displays whether encryption is enabled or disabled/unverified.
- Records findings using Lynis reporting functions.
- Reports potential security concerns when the status indicates a problem.

### Example output

```text
[+] Custom tests
  - FileVault disk encryption                     [ ENABLED ]
```

The output above is illustrative. Actual results depend on the system configuration.

## Enhancement 2: macOS Firewall Audit

**Test ID:** `FIRE-9001`

The macOS Application Firewall helps control incoming network connections. Stealth mode can reduce the system's visibility to certain unsolicited network probes.

This test uses the following utility:

```text
/usr/libexec/ApplicationFirewall/socketfilterfw
```

### Checks performed

- Checks whether the Application Firewall utility is available.
- Retrieves the global firewall state.
- Checks whether stealth mode is enabled.
- Records results in the Lynis log and report.
- Reports potential security concerns when a setting is disabled or cannot be verified.

### Example output

```text
[+] Custom tests
  - macOS Application Firewall                     [ ENABLED ]
  - Firewall stealth mode                          [ ENABLED ]
```

This output is illustrative; replace it with actual results from your audit when documenting a completed run.

## Requirements

- A compatible macOS system.
- Lynis installed locally or checked out from its official repository.
- Terminal access.
- Permission to run Lynis audits.
- The macOS `fdesetup` and `socketfilterfw` utilities for the respective checks.

These tests are designed for macOS and are not intended to run unchanged on Linux systems.

## Installation and Usage

### 1. Obtain Lynis

Clone the official Lynis repository:

```bash
git clone https://github.com/CISOfy/lynis.git
cd lynis
```

If Lynis is already installed, navigate to your existing Lynis directory instead.

### 2. Back up the custom-test file

Before modifying an existing custom-test file, create a backup:

```bash
sudo cp include/tests_custom include/tests_custom.backup
```

### 3. Configure the custom tests

Lynis loads its custom tests through the `include/tests_custom` file when the custom-test stage is reached.

Ensure the required test code is included in that file and that the test IDs are unique. The standalone firewall script must also be integrated into the appropriate loading mechanism if you want it to execute automatically.

### 4. Check the Lynis version

```bash
sudo ./lynis show version
```

### 5. Run the security audit

```bash
sudo ./lynis audit system
```

Review the terminal output and Lynis log to verify whether the custom checks executed.

### 6. Inspect the audit log

```bash
sudo grep -E 'FIRE-9001|CRYP-9001|FileVault|Firewall|Stealth' \
    /var/log/lynis.log
```

The log path can vary depending on the Lynis configuration.

## Custom Test Functions

The custom checks use Lynis functions to integrate with the audit workflow:

| Function | Purpose |
|---|---|
| `Register` | Defines a custom test and its identifier. |
| `Display` | Shows a result in the terminal. |
| `LogText` | Records test information in the Lynis log. |
| `Report` | Adds information to the Lynis report data. |
| `ReportWarning` | Records a potential security concern. |
| `AddHP` | Contributes hardening points according to the test logic. |

Function signatures and supported behavior should be checked against the installed Lynis version.

## Testing and Validation

To validate the enhancements:

1. Run a standard Lynis audit.
2. Verify that the custom-test stage is reached.
3. Confirm that `CRYP-9001` executes and reports the FileVault status.
4. Confirm that `FIRE-9001` executes and reports firewall and stealth-mode status.
5. Inspect the terminal output and audit log.
6. Capture screenshots of the actual results.
7. Test behavior when a command is unavailable or a setting cannot be verified.

A test file existing on disk does not by itself prove that Lynis loaded or executed it. Verify execution before claiming a successful audit.

## Learning Outcomes

- Understanding the Lynis custom-test architecture.
- Developing shell-based security checks.
- Inspecting macOS disk-encryption configuration.
- Auditing Application Firewall settings.
- Recording findings through security-audit reporting functions.
- Troubleshooting permissions and test-loading issues.
- Understanding the importance of validating security results.

## Limitations

- The checks are designed specifically for macOS.
- Results depend on the availability and behavior of system utilities.
- An unknown or unreadable status should not automatically be interpreted as a confirmed security failure.
- The tests provide configuration checks, not a complete security assessment.
- Custom-test execution must be verified for the installed Lynis version.

## References

- [Official Lynis repository](https://github.com/CISOfy/lynis)
- [Lynis documentation](https://cisofy.com/documentation/lynis/)
- [Apple Platform Security](https://support.apple.com/guide/security/welcome/web)

##
