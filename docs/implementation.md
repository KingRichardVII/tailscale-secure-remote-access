# Implementation — Secure Remote Access with Tailscale

This document records the operations actually performed in Lab 01.5. Commands use generic placeholders for endpoint addresses where relevant.

## Baseline and installation

The Ubuntu VM already had hardened OpenSSH, an Ed25519 desktop administrative key, and active UFW with default-deny incoming traffic. The initial SSH service was reachable only through the configured trusted LAN policy.

Tailscale 1.104.1 was installed on Ubuntu from its official APT repository. `tailscaled` was verified active. The Ubuntu VM was enrolled with:

```bash
sudo tailscale up --ssh=false --accept-dns=false --accept-routes=false
```

This preserved OpenSSH as the server's SSH implementation, avoided replacing Ubuntu's DNS configuration through Tailscale, and avoided accepting advertised routes. No subnet route or exit node was advertised.

## Tailnet controls

1. Enabled manual device approval in the Tailscale admin console.
2. Reviewed and deleted the initial all-users/all-devices/all-ports access rule.
3. Removed the default Tailscale SSH policy rule because standard OpenSSH was retained.
4. Enrolled and approved Ubuntu (`linux01`).
5. Installed Tailscale on the Windows laptop, enrolled it, captured the `Needs approval` state, and manually approved it.
6. From Windows PowerShell, observed that TCP/22 was initially unreachable through the tailnet.
7. Added a grant with one source (laptop Tailscale IP), one destination (Ubuntu Tailscale IP), and `tcp:22`.
8. Repeated the test and observed TCP/22 become reachable.

The general shape of the grant is in [`../configs/ssh-grant-example.json`](../configs/ssh-grant-example.json). Its angle-bracket strings are placeholders; replace them with actual Tailscale selectors to make a functional policy. This is not a full tailnet policy export and must not be used blindly to overwrite another tailnet's rules.

## Dedicated laptop SSH key

On the laptop, an independent passphrase-protected Ed25519 key was generated:

```powershell
ssh-keygen -t ed25519 -a 100 -f "$env:USERPROFILE\.ssh\id_ed25519_homelab" -C "laptop-homelab"
```

Only its `.pub` content was transferred to the pre-authorized desktop session and appended over SSH to Ubuntu's `~/.ssh/authorized_keys` without overwriting the existing desktop key.

On Ubuntu, the resulting permissions were verified:

```text
~/.ssh                 700
~/.ssh/authorized_keys 600
```

The SSH host key fingerprint was compared with the fingerprint retrieved over the already trusted desktop SSH path before the new laptop host key was trusted.

A successful connection used standard OpenSSH over the Ubuntu Tailscale IPv4 address:

```powershell
ssh -o IdentitiesOnly=yes -i "$env:USERPROFILE\.ssh\id_ed25519_homelab" richard@<UBUNTU_TAILSCALE_IPV4>
```

## External network validation

The Windows laptop disconnected from home Wi-Fi and used a phone's cellular hotspot. Over this separate network, the operator verified:

```powershell
Test-NetConnection <UBUNTU_TAILSCALE_IPV4> -Port 22
```

`TcpTestSucceeded` was `True`, and SSH login succeeded using the dedicated laptop key. Within Ubuntu, `hostname`, `whoami`, and `echo "$SSH_CONNECTION"` confirmed server identity, account, and SSH endpoint roles.

## Ungranted port and cleanup

A temporary Python HTTP listener was started bound specifically to Ubuntu's Tailscale address:

```bash
mkdir -p /tmp/tailscale-policy-test
python3 -m http.server 8080 --bind <UBUNTU_TAILSCALE_IPV4> --directory /tmp/tailscale-policy-test
```

From the laptop, `Test-NetConnection <UBUNTU_TAILSCALE_IPV4> -Port 8080` returned `False`. The HTTP server was stopped with `Ctrl+C`. No listener appeared in the final TCP/8080 check.

Finally, `systemctl is-active tailscaled ssh`, `sudo ufw status verbose`, and `tailscale status` were inspected. The original LAN-only UFW policy remained present. The HTTP test directory under `/tmp` contained no sensitive data.

## Important interpretation

`TcpTestSucceeded : False` means the TCP connection could not be established. This test by itself does not prove whether an access grant, the local firewall, or another network control was the decisive blocker. The before/after TCP/22 result provides stronger evidence that the Tailscale policy change affected SSH reachability.
