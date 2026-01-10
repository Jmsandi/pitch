# 🚀 Quick Bot Infrastructure Setup

This is the simplified guide to get your bot running with a domain and SSL.

---

## 1. Domain Setup
In your domain registrar (GoDaddy, etc.), point **www.geneline-x.net** to IP **143.198.17.145** (A Record).

> [!NOTE]
> **Multi-User Isolation**: Even if other users are on this VPS, Nginx uses your domain name (`www.geneline-x.net`) like a private address. It will only send traffic meant for YOUR domain to YOUR app folder.

---

## 2. Server Setup (One Command)
SSH into your VPS and run:
```bash
cd /opt/whatsapp-geneline-bridge
sudo chmod +x scripts/setup-infrastructure.sh
sudo ./scripts/setup-infrastructure.sh
```

---

## 3. Enable SSL (HTTPS)
Run this command on the VPS and follow the prompts:
```bash
sudo certbot --nginx -d www.geneline-x.net
```
*Choose "Redirect" when asked.*

---

## 4. Setup Auto-Deployment (CI/CD)
1. On **VPS**, get your private key: `cat ~/.ssh/id_rsa` (or create one with `ssh-keygen`).
2. Add your **Public Key** to `~/.ssh/authorized_keys` on VPS.
3. In **GitHub Settings** → **Secrets**, add:
   - `VPS_HOST`: `143.198.17.145`
   - `VPS_USER`: `root`
   - `VPS_SSH_KEY`: (Your Private Key)

**Done!** Every time you push to GitHub, the bot will update automatically.
