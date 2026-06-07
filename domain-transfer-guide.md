# Transferring a Domain from GKG to INWX + GitHub Pages Setup

A step-by-step guide based on real-world troubleshooting.

---

## 1. Prepare GitHub Pages

- In your GitHub repo settings → Pages, set the source branch (e.g. `main`)
- Add your custom domain (e.g. `yourdomain.com`) and save — this creates a `CNAME` file in your repo

---

## 2. Initiate the Domain Transfer

- In GKG, unlock the domain and get the **Auth/EPP code**
- In INWX, go to Domains → Transfer, enter the domain and Auth code
- Approve the transfer email when it arrives
- Wait for the transfer confirmation email from INWX

---

## 3. Update the NS Delegation in INWX

- Go to **Domains → Domain List**
- Click on the domain name to open **Domain Information**
- Click **"External Nameserver"** and switch to **INWX nameserver** in the popup
- Save — this pushes `ns.inwx.es`, `ns2.inwx.es`, `ns3.inwx.eu` to the `.com` registry
- Verify it's in progress: status should show **"UPDATE REQUESTED"**

---

## 4. Verify the Delegation Propagated

```bash
dig NS yourdomain.com @a.gtld-servers.net +short
```

Wait until this returns INWX's nameservers instead of GKG's. Usually takes minutes, up to a few hours.

---

## 5. Configure DNS Zone Records in INWX

Go to **Nameserver → Nameserver entries** → Add/clone domain, then add the following records:

| Name    | Type  | Value                    |
|---------|-------|--------------------------|
| (blank) | A     | 185.199.108.153          |
| (blank) | A     | 185.199.109.153          |
| (blank) | A     | 185.199.110.153          |
| (blank) | A     | 185.199.111.153          |
| *       | A     | 185.199.108.153          |
| *       | A     | 185.199.109.153          |
| *       | A     | 185.199.110.153          |
| *       | A     | 185.199.111.153          |
| www     | CNAME | yourusername.github.io   |

---

## 6. Verify DNS Is Resolving Correctly

```bash
# Should return all 4 GitHub IPs
dig yourdomain.com +short

# Should return yourusername.github.io then the IPs
dig www.yourdomain.com +short
```

---

## 7. Finalize GitHub Pages

- Go to repo settings → Pages → Custom domain
- If the DNS check doesn't pass automatically, remove the domain and re-add it to trigger a fresh check
- Once DNS check passes, enable **"Enforce HTTPS"** when it becomes available (may take a few minutes for Let's Encrypt to provision the certificate)

---

## 8. Flush Local DNS Cache and Test

```bash
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

Then open the site in a normal browser window.

---

## Key Things to Remember

- **NS delegation** (step 3) and **DNS zone records** (step 5) are two separate things in INWX — don't confuse them
- Always verify delegation at the registry level with `@a.gtld-servers.net`, not just with a regular `dig`
- Don't delete GKG zone records until **after** the transfer is complete and INWX delegation is confirmed
- `www` should be a **CNAME only** — not an A record

---

## Useful Diagnostic Commands

```bash
# Check what nameservers the .com registry has on file
dig NS yourdomain.com @a.gtld-servers.net +short

# Full delegation trace
dig NS yourdomain.com +trace

# Check resolution via a public resolver (bypasses local cache)
dig yourdomain.com @1.1.1.1

# Check who the registrar is and what NSes are registered
whois yourdomain.com | grep -E "Registrar|Name Server"

# Flush local DNS cache on macOS
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```