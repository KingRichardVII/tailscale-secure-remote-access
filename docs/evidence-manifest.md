# Screenshot Evidence Manifest — Lab 01.5

These are **sanitized public copies** derived from private original screenshots captured during the exercise. Preserve unmodified originals in private homelab records. Do not publish the originals.

| Published filename | Evidence | Lab notes / corresponding private label |
|---|---|---|
| `device-approval-setting.png` | Manual device approval enabled | Device management security setting |
| `device-pending-approval.png` | Laptop shows `Needs approval` | **Lab 01.5 — Tailscale Device Authorization: Pending Approval** |
| `deny-by-default-policy.png` | No general access grants after removing default allow-all | Initial Tailscale access-policy remediation |
| `laptop-ssh-grant.png` | Laptop-to-Ubuntu TCP/22-only grant | **Lab 01.5 — Tailscale Least-Privilege Access Policy: SSH Only** |
| `ssh-before-grant-denied.png` | TCP/22 connection `False` before grant | **Lab 01.5 — Zero Trust Access Validation: SSH Denied Before Grant** |
| `ssh-after-grant-allowed.png` | TCP/22 connection changes from `False` to `True` | **Lab 01.5 — Zero Trust Access Validation: SSH Allowed After Grant** |
| `external-network-tcp22-reachable.png` | TCP/22 reachable over cellular hotspot | **Lab 01.5 — External Network: Tailscale SSH Connectivity** |
| `external-network-ssh-authenticated.png` | `hostname`, `whoami`, SSH connection environment | **Lab 01.5 — Secure Remote SSH Access: External Network Validation** |
| `ungranted-port-denied.png` | TCP/8080 connection `False` | **Lab 01.5 — Least-Privilege Network Policy: Unauthorized Port Denied** |
| `post-test-health-check.png` | Active SSH and Tailscale, original UFW policy, no TCP/8080 listener | Final health verification |

## Privacy processing

Screenshots were re-encoded without carrying forward original image metadata. Opaque redactions or crops mask unnecessary account names/email, laptop identifiers, exact Tailscale endpoint addresses, Windows profile paths, and any externally routable peer address. Port numbers, status messages, test outcomes, and essential security policy settings remain visible.

**Important:** These files need a final full-resolution human review before first public upload. Sanitization reduces avoidable information exposure but does not guarantee that the public information is risk-free. Never include authentication URLs, private SSH keys, passphrases, tokens, or unpublished home public IP information.
