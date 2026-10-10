# Troubleshooting and Lessons Learned

## Clipboard contents changed during public-key transfer

**Issue:** A desktop PowerShell validation command rejected the expected laptop public key. Another command also initially ran on the wrong Windows computer, so the laptop-only key file could not be found.

**Cause and observation:** The `.pub` key was generated on the laptop, while the desktop was the trusted administrative path to Ubuntu. Copying a command from the instructions replaced the public-key contents on the clipboard. Additionally, the laptop key file was not supposed to exist at the desktop's local `.ssh` path.

**Resolution:** Explicitly separated instructions by physical machine. Transferred **only the public key** to the desktop, captured it into a persistent PowerShell variable, validated its expected Ed25519 structure, and appended it through the existing authorized SSH session to Ubuntu. Subsequent key-count and permission checks succeeded. The final SSH login from the laptop worked.

**Lesson:** Clipboard-based workflows are easy to disrupt. Always state which device is executing a step, and verify sensitive or security-critical inputs before changing server configuration. Prefer direct file transfer where convenient and authorized.

## Distinguishing network policy from authentication

**Observation:** A Tailscale device could be visible in `tailscale status` while the SSH connection timed out. After adding the exact TCP/22 grant, the TCP test succeeded, but SSH still required a valid Ed25519 key.

**Lesson:** Device enrollment, network authorization, and application authentication are separate controls. Passing one stage does not imply permission at the next.

## Interpreting the denied port 8080 test

**Observation:** The laptop's ICMP ping succeeded, but TCP/8080 failed while a temporary HTTP server had been started. The test was performed after TCP/22 was authorized and reachable.

**Limitation:** The host used both UFW and Tailscale's Linux firewall integration. The observed denial is compatible with the narrow Tailscale grant but was not independently attributed to one specific filtering mechanism.

**Lesson:** Report what was observed. A blocked test alone is not a complete proof of the responsible enforcement layer.
