## 🛡️ UFW SSH Configuration: Avoiding Overlapping Rules

When configuring the Uncomplicated Firewall (UFW) for SSH, it is critical to avoid stacking redundant rules. UFW processes rules sequentially from the top down, meaning the **first matching rule wins** and subsequent rules are ignored for that packet.

### The Problem with Multiple SSH Rules

If you run `sudo ufw show added` or `sudo ufw status` and see multiple overlapping rules for SSH, your configuration is likely broken.

| Rule | Behavior | Issue |
| --- | --- | --- |
| `ufw allow OpenSSH` | Allows TCP traffic on port 22 via app profile. | **Shadowing:** If this rule is listed first, it catches all SSH traffic, rendering stricter rules below it useless. |
| `ufw limit 22/tcp` | Allows TCP on port 22 + **Rate Limiting** (drops connections if an IP tries 6+ times in 30s). | **Recommended:** Protects against brute-force attacks, but fails if shadowed by a standard `allow` rule. |
| `ufw allow 22` | Allows TCP *and* UDP on port 22. | **Insecure:** SSH does not use UDP. This needlessly increases the attack surface. |
| `ufw allow 2222` | Allows TCP *and* UDP on port 2222. | **Insecure:** Same issue as above, just on an alternate port. |

### The Fix: Enforcing Rate Limits and Removing Clutter

To properly secure your SSH service, you must delete the redundant `allow` rules so the `limit` rule can actively protect the server from automated brute-force attacks.

**Scenario A: Running SSH on Default Port (22)**
Keep the TCP rate-limit rule and delete the rest.

```bash
# Add the secure rate-limiting rule
sudo ufw limit 22/tcp

# Remove redundant and insecure rules
sudo ufw delete allow OpenSSH
sudo ufw delete allow 22
sudo ufw delete allow 2222

```

**Scenario B: Running SSH on Alternate Port (e.g., 2222)**
Apply the TCP rate-limit to your custom port and delete the rest.

```bash
# Add the secure rate-limiting rule to the custom port
sudo ufw limit 2222/tcp

# Remove redundant and insecure rules
sudo ufw delete allow OpenSSH
sudo ufw delete allow 22
sudo ufw delete allow 2222

```

> **Note:** Always verify your active firewall processing order by running `sudo ufw status numbered`.
