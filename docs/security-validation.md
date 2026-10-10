# Security Validation — Lab 01.5

| Control / test | Method | Observed result | Conclusion |
|---|---|---|---|
| Manual device approval | Laptop enrolled before admin approval | Admin console showed `Needs approval` | Device enrollment required explicit approval |
| Deny-by-default baseline | Remove initial all-to-all grant | Admin console showed no policy grants | No broad general grants remained |
| Network authorization before SSH grant | Windows `Test-NetConnection` to Ubuntu TCP/22 | `False` | Connection not established |
| Scoped grant | One laptop IP to one Ubuntu IP, `tcp:22` | Grant saved in console | Specific TCP/22 access authorized |
| Network authorization after SSH grant | Repeat same TCP/22 connection test | `True` | Connection established after policy change |
| Separate laptop credential | Dedicated Ed25519 key registered | Login successful | Laptop SSH key was accepted |
| SSH server identity | SHA256 host fingerprint compared over trusted channel | Fingerprints matched | New Tailscale endpoint was verified as intended Ubuntu host |
| External connectivity | Laptop on cellular hotspot | TCP/22 `True`; SSH login successful | Remote access worked away from home LAN |
| Unpermitted TCP/8080 | Temporary HTTP listener on server Tailscale IP | TCP/8080 `False` | Connection unsuccessful; blocking layer not independently isolated |
| Cleanup | Stop HTTP server, inspect TCP listener | No TCP/8080 listener | Temporary test server stopped |
| Service health | `systemctl is-active tailscaled ssh` | Both `active` | Services remained running |
| Existing firewall state | `sudo ufw status verbose` | Active, default deny incoming, trusted-LAN SSH allowance | Baseline UFW configuration remained |

## Important non-claims

- The access grant did not prove the host's firewall was unnecessary or irrelevant.
- Ping may work while a TCP port is denied; ICMP and TCP are distinct protocols.
- One external-network test does not show reliability from every network.
- No penetration test, router port scan, or full audit of exposed services was performed.
- No direct Proxmox access was authorized over this tailnet.
- Using an exact device Tailscale IP as a policy selector is functional here but may require updating after device changes.
