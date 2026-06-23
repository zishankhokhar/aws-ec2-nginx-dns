# 🚀 AWS EC2 + NGINX + Custom Domain via DNS

Deployed a web server on AWS EC2 running NGINX, configured a custom domain using Cloudflare DNS, and verified end-to-end HTTP access via the domain.

**Live URL:** `http://zishankhokhar.co.uk`

---

## 🏗️ Architecture

```
Browser → DNS Lookup → Cloudflare (A Record) → EC2 Public IP → NGINX (port 80)
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| AWS EC2 | Virtual server (Amazon Linux 2 / Ubuntu) |
| NGINX | Web server |
| Cloudflare | Domain registrar + DNS management |
| AWS Security Groups | Firewall — allow port 80 (HTTP) and 22 (SSH) |

---

## ⚙️ Setup

### 1. Launch EC2 Instance

- AMI: Amazon Linux 2 (or Ubuntu)
- Instance type: `t2.micro` (free tier)
- Security Group inbound rules:
  - Port `22` — SSH (your IP)
  - Port `80` — HTTP (0.0.0.0/0)

### 2. Install & Start NGINX

SSH into the instance:

```bash
ssh -i "your-key.pem" ec2-user@<EC2-PUBLIC-IP>
```

**Amazon Linux 2:**
```bash
sudo yum install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
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
| A | `@` or `nginx` | `<EC2 Public IP>` | Auto |

### 4. Verify

```bash
# Check DNS has propagated
dig yourdomain.com +short

# Test HTTP response
curl -I http://yourdomain.com
```

Then visit `http://yourdomain.com` in a browser — you should see the NGINX welcome page.

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
│   └── nginx-default.conf    # NGINX config reference
└── screenshots/
    └── README.md             # Screenshot guide
```

---

## 🔗 Related

- 📚 **[DevOps Networking Notes](https://github.com/zishankhokhar/devops-networking-notes)** — the theory behind this project
- [Cloudflare DNS Docs](https://developers.cloudflare.com/dns/)
- [AWS EC2 Docs](https://docs.aws.amazon.com/ec2/)
- [NGINX Beginner's Guide](https://nginx.org/en/docs/beginners_guide.html)
