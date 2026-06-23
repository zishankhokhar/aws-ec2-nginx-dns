# 🚀 AWS EC2 + NGINX + Custom Domain via DNS

Deployed a web server on AWS EC2 running NGINX, configured a custom domain using Cloudflare DNS, and verified end-to-end HTTP access via the domain.

**Live URL:** `http://zishankhokhar.co.uk`

---

## 🏗️ Architecture

```
Browser → DNS Lookup → Cloudflare (A Record) → 35.179.123.0 → NGINX (port 80)
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| AWS EC2 | Virtual server (Ubuntu 24.04 LTS, t3.micro) |
| NGINX | Web server |
| Cloudflare | Domain registrar + DNS management |
| AWS Security Groups | Firewall — allow port 80 (HTTP) and 22 (SSH) |

---

## ⚙️ Setup

### 1. Launch EC2 Instance

- AMI: Ubuntu 24.04 LTS
- Instance type: `t3.micro` (free tier eligible)
- Security Group inbound rules:
  - Port `22` — SSH (your IP)
  - Port `80` — HTTP (0.0.0.0/0)

### 2. Install & Start NGINX

SSH into the instance:

```bash
ssh -i "zishan-devops-key.pem" ubuntu@35.179.123.0
```
**Ubuntu:**
```bash
sudo apt update && sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

Verify locally:
```bash
curl http://localhost
```

### 3. Configure DNS (Cloudflare)

1. Cloudflare Dashboard → your domain → DNS → Records
2. Add a new record:

| Type | Name | Value | TTL |
|---|---|---|---|
| A | `@` | `35.179.123.0` | Auto |

### 4. Verify

```bash
# Check DNS has propagated
dig zishankhokhar.co.uk +short

# Test HTTP response
curl -I http://zishankhokhar.co.uk
```

Then visit `http://zishankhokhar.co.uk` in a browser — you should see the NGINX welcome page.

---

## 🔧 Technical Decisions

- **Ubuntu 24.04 LTS over Amazon Linux** — Ubuntu has a larger community, better documentation and is more commonly used in production environments making it a practical choice
- **Cloudflare over Route53** — Cloudflare offers free DNS management with a fast global network and simple interface, ideal for this setup
- **t3.micro over t2.micro** — t3.micro is the newer generation, offers better performance and is free tier eligible in eu-west-2 (London)
- **DNS only (grey cloud) in Cloudflare** — disabled Cloudflare proxy initially to test raw DNS resolution directly to the EC2 IP, confirming the setup works at the infrastructure level
- **Security Group over ufw** — used AWS Security Groups as the firewall rather than configuring ufw on the instance, keeping security managed at the cloud infrastructure level

---

## ⌨️ Commands Used

```bash
# Connect to EC2 instance
ssh -i "zishan-devops-key.pem" ubuntu@35.179.123.0

# Update package list
sudo apt update

# Install NGINX
sudo apt install -y nginx

# Enable NGINX on boot
sudo systemctl enable nginx

# Start NGINX
sudo systemctl start nginx

# Check NGINX status
sudo systemctl status nginx

# Test NGINX is serving locally
curl http://localhost

# Verify DNS has propagated
dig zishankhokhar.co.uk +short

# Test HTTP response from domain
curl -I http://zishankhokhar.co.uk
```
---

## 🐛 Issues & Fixes

| Problem | Fix |
|---|---|
| Instance type showed t3.micro not t2.micro | t3.micro is newer and also free tier eligible in eu-west-2 |
| SSH key permissions error | Ran `chmod 400 zishan-devops-key.pem` to fix permissions |
| Couldn't find Allow SSH checkbox in Network Settings | SSH rule was already there by default, just needed to add HTTP rule manually |

---

## 📸 Screenshots

See the [`/screenshots`](./screenshots/) folder:

- EC2 instance running in AWS console
- `sudo systemctl status nginx` output
- Cloudflare DNS A record configured
- Domain loading NGINX page in browser
- `dig` and `curl` output confirming resolution

---

## 📂 Repo Structure

```
aws-ec2-nginx-dns/
├── README.md
├── configs/
│   └── nginx-default.conf        # NGINX config reference
└── screenshots/
    ├── README.md                 # Screenshot guide
    ├── 01-ec2-running.png        # EC2 instance running
    ├── 02-nginx-status.png       # NGINX service status
    ├── 03-dns-a-record.png       # Cloudflare DNS A record
    ├── 04-dig-output.png         # dig command output
    ├── 05-curl-output.png        # curl command output
    └── 06-domain-browser.png     # Domain loading in browser
```
---

## 🔗 Related

- 📚 **[DevOps Networking Notes](https://github.com/zishankhokhar/devops-networking-notes)** — the theory behind this project
- [Cloudflare DNS Docs](https://developers.cloudflare.com/dns/)
- [AWS EC2 Docs](https://docs.aws.amazon.com/ec2/)
- [NGINX Beginner's Guide](https://nginx.org/en/docs/beginners_guide.html)
