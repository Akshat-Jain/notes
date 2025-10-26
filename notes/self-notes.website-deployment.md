---
id: domains-and-subdomains
title: How my domain and subdomains work
desc: ''
updated: 1761493304862
created: 1761460997313
---

There are 3 components:
1. Squarespace: This is the domain registrar. Domain is a piece of land, Squarespace is the land registry. It's where you go and pay the fees for the ownership of the land/domain.
2. Netlify: This is the DNS Host and Hosting Provider.
3. GitHub: Stores the code.

### Squarespace

I bought my domain (`akjn.dev`) from Google Domains, which got migrated to Squarespace. So, Squarespace is my new domain registrar.

Apart from being the domain registrar, I use it for 2 things:

**Custom Nameservers**

- Delegating DNS resolution to Netlify's DNS Nameservers. This gets used for subdomains like `notes.akjn.dev`.
- I could have used Squarespace nameservers for DNS resolution, but Netlify is where the build and deploy is happening so I prefer to use their nameservers. It also decouples my DNS resolution from Squarespace, as I hate Squarespace and would like to move away from it sometime in the future.

| ![](/assets/images/netlify-nameservers.png) | 
|:--:| 
| *Netlify nameservers* |

<br>

| ![](/assets/images/squarespace-custom-nameservers.png) | 
|:--:| 
| *Squarespace using Netlify nameservers* |

<br>

**Domain Forwarding**

- Redirecting subdomains to the actual URLs. This gets used for subdomains like `chess.akjn.dev`, which gets redirected to `https://lichess.org/@/akjn`.

### Netlify

- DNS Host: Authoritative DNS provider.
    - Manages all DNS records for `*.akjn.dev`.
    - Nameservers: `dns1.p07.nsone.net`, `dns2.p07.nsone.net`, `dns3.p07.nsone.net`, `dns4.p07.nsone.net`
- Hosting Provider.
    - Serves multiple projects under different domains. Example: `gorgeous-tapioca-ffe69d.netlify.app` is served under `notes.akjn.dev`
    - Hosts website files on CDN.
- Build automation: Watches GitHub repo via webhooks, automatically builds and deploys on git push.


| ![](/assets/images/netlify-different-projects.png) | 
|:--:| 
| *Netlify: Multiple projects under different domains / subdomains* |

<br>

| ![](/assets/images/netlify-dns-records.png) | 
|:--:| 
| *DNS records on Netlify (trimmed down version via inspect element)* |

### The Complete Deployment Architecture

```
                    ┌──────────────────────┐
                    │   SQUARESPACE        │
                    │   (Registrar)        │
                    │                      │
                    │ • Domain: akjn.dev   │
                    └──────────┬───────────┘
                               │
                               │
                               ↓
                    ┌──────────────────────--┐
                    │    NAMESERVERS         │
                    │    dns1.p07.nsone.net  │
                    │    dns2.p07.nsone.net  │
                    │    dns3.p07.nsone.net  │
                    │    dns4.p07.nsone.net  │
                    │                        │
                    │ ☝️ Netlify nameservers │
                    └──────────┬───────────--┘
                               │
              ┌────────────────┴────────────────┐
              ↓                                 ↓
   ┌──────────────────────┐         ┌──────────────────────-----┐
   │   NETLIFY DNS        │         │   SQUARESPACE FORWARDING  │
   │   (Authoritative)    │         │      (HTTP redirects)     │
   │                      │         │ • chess.akjn.dev          │  
   │ • notes.akjn.dev     │         │ • book-club.akjn.dev      │
   │ • chess.akjn.dev     │         └───────────────────────────┘
   │ • akjn.dev           │         
   └──────────┬───────────┘         
              │                     
              ↓
   ┌─────────────────────-─┐
   │   NETLIFY HOSTING     │
   │   + GitHub            │
   │                       │
   │ Push → Build → Deploy │
   └──────────────────────-┘
```

### The Critical Delegation: Nameservers

Even though Squarespace owns the domain registration, DNS authority is **delegated** to Netlify through nameservers.

**Verification:**
```bash
$ dig NS akjn.dev

;; ANSWER SECTION:
akjn.dev.    3600    IN    NS    dns1.p07.nsone.net.
akjn.dev.    3600    IN    NS    dns2.p07.nsone.net.
akjn.dev.    3600    IN    NS    dns3.p07.nsone.net.
akjn.dev.    3600    IN    NS    dns4.p07.nsone.net.
```

All nameservers point to Netlify (nsone.net) → **Netlify controls ALL DNS**

### The Two-Layer System (DNS resolution by Netlify + Domain Forwarding by Squarespace)

**Layer 1: DNS (Network Layer)**
- **Purpose**: Route network traffic to correct IP addresses
- **Controlled by**: Netlify (via nameservers)
- **Handles**: Domain → IP translation

**Layer 2: Application/HTTP (Service Layer)**
- **Purpose**: Determine what content to serve or where to redirect
- **Controlled by**: Receiving server (Squarespace or Netlify)
- **Handles**: Virtual host routing, HTTP redirects, content selection

| ![](/assets/images/squarespace-forwarding-rules.png) | 
|:--:| 
| *Squarespace forwarding rules* |

### Example Flows

**Flow 1: notes.akjn.dev (Netlify-hosted)**

1. User visits `notes.akjn.dev`
2. DNS (Layer 1 - Netlify):
   → `notes.akjn.dev` → CNAME → `gorgeous-tapioca-ffe69d.netlify.app`
   → Resolves to IPs: 13.215.239.219, 52.74.6.109
3. Browser connects to Netlify's servers
4. HTTP (Layer 2 - Netlify):
   → Reads Host header: `notes.akjn.dev`
   → Routes to project: `gorgeous-tapioca-ffe69d`
   → Serves website content ✅

**Verification:**
```bash
$ dig notes.akjn.dev

;; ANSWER SECTION:
notes.akjn.dev.                      3600    IN    CNAME    gorgeous-tapioca-ffe69d.netlify.app.
gorgeous-tapioca-ffe69d.netlify.app. 1       IN    A        13.215.239.219
gorgeous-tapioca-ffe69d.netlify.app. 1       IN    A        52.74.6.109
```

**Flow 2: chess.akjn.dev (Squarespace Domain Forwarding)**

1. User visits `chess.akjn.dev`
2. DNS (Layer 1 - Netlify):
   → `chess.akjn.dev` → CNAME → `ext-sq.squarespace.com`
   → Resolves to Squarespace IPs: 198.185.159.144, etc.
3. Browser connects to Squarespace's servers
4. HTTP (Layer 2 - Squarespace):
   → Reads Host header: `chess.akjn.dev`
   → Checks redirect config database
   → Finds: `chess.akjn.dev` → `https://lichess.org/@/akjn`
   → Sends HTTP 301 redirect
5. Browser follows redirect to Lichess ✅

**Verification:**
```bash
$ dig chess.akjn.dev

;; ANSWER SECTION:
chess.akjn.dev.         10      IN      CNAME   ext-sq.squarespace.com.
ext-sq.squarespace.com. 105 IN A    198.185.159.144
ext-sq.squarespace.com. 105 IN A    198.49.23.145
# ... more IPs
```

| ![](/assets/images/squarespace-forwarding-rule-example.png) | 
|:--:| 
| *Forwarding rule for chess.akjn.dev in Squarespace* |

### Experiment 1: myanimelist.akjn.dev

1. I had a forwarding rule in Squarespace for `myanimelist.akjn.dev` -> `https://myanimelist.net/profile/akjn`.
2. I deleted the forwarding rule.
3. `myanimelist.akjn.dev` stopped working immediately, and started showing a Squarespace error page saying "We're under construction. Please check back for an update soon." page.
4. I added back the forwarding rule and `myanimelist.akjn.dev` started working again immediately.
5. The stopped working / started working was immediate since it had nothing to do with DNS (hence no waiting for DNS propagation etc), it was just a forwarding rule entry deleted from Squarespace's system which immediately gets reflected.

DNS continued to work after I deleted the forwarding rule:
```bash
$ dig myanimelist.akjn.dev +short
ext-sq.squarespace.com.
198.185.159.144
198.49.23.145
```

Hence, layer 1 continued to work but layer 2 was broken.

Also, the results are kinda obvious considering that Netlify only contains the DNS record for `myanimelist.akjn.dev`, it doesn't know that it's pointed to `https://myanimelist.net/profile/akjn`.

| ![](/assets/images/netlify-myanimelist-dns-record.png) | 
|:--:| 
| *DNS Record for myanimelist.akjn.dev in Netlify* |

<br>

**Analysis:**

```
Layer 1 - DNS (Still Working):
  ✅ Netlify DNS resolves myanimelist.akjn.dev
  ✅ Points to ext-sq.squarespace.com
  ✅ Browser connects to Squarespace servers

Layer 2 - Application (Broken):
  ✅ Squarespace receives HTTP request
  ✅ Reads Host header: myanimelist.akjn.dev
  ❌ Checks internal database: NOT FOUND!
  ❌ No redirect configuration exists
  ❌ Shows default parking page
```

### Experiment 2: react.akjn.dev

1. I had a custom DNS record for `react.akjn.dev` pointed to `spectacular-bonbon-62f33a.netlify.app` in both Netlify and Squarespace.
2. Since we've set up DNS resolution to be Netlify's responsibility, the DNS record in Squarespace seemed redundant, and more importantly, useless.
3. I removed that record from Squarespace, and the site indeed continues to work, so the Squarespace record was redundant and useless indeed.

### Experiment 3: Verifying that DNS is indeed being handled by Netlify

<br>

**Test 1: Check who responds to DNS queries**

```bash
# Query Netlify's nameserver directly (trimmed down response)
$ dig @dns1.p07.nsone.net x.akjn.dev

;; flags: qr aa rd;

;; ANSWER SECTION:
x.akjn.dev.    10    IN    CNAME    ext-sq.squarespace.com.
```

The `aa` flag (as can be seen in the `flags` section) proves Netlify's nameserver is authoritative. `aa` stands for `Authoritative Answer`.

**Test 2: Check TTL values**

| Location | TTL | What DNS Returns |
|----------|-----|------------------|
| Netlify DNS | 10 seconds | ✅ 10 seconds |
| Squarespace DNS | 4 hours | ❌ Not returned |

<br>

The actual DNS responses use Netlify's TTL values (as can be seen in the `ANSWER SECTION` part of the above `dig` command's output), proving Netlify is authoritative.

**Test 3: Check ext-sq.squarespace.com is globally accessible**

```bash
# Query from Google DNS
$ dig @8.8.8.8 ext-sq.squarespace.com +short
198.49.23.144
198.49.23.145
198.185.159.144
198.185.159.145

# Query from Cloudflare DNS gives the same IPs
$ dig @1.1.1.1 ext-sq.squarespace.com +short
198.49.23.144
198.49.23.145
198.185.159.144
198.185.159.145
```

**Key Finding**: `ext-sq.squarespace.com` is managed by Squarespace's GLOBAL DNS infrastructure, NOT affected by our custom Squarespace DNS settings. Anyone can CNAME to it from any DNS provider!

Why is that important?
- Step 1 (Your DNS): A browser asks Netlify's DNS, "Where is `x.akjn.dev`?" Netlify replies, "It's an alias for `ext-sq.squarespace.com`."
- Step 2 (Global DNS): The browser then asks the public internet, "Okay, where is `ext-sq.squarespace.com`?" The global DNS system replies with Squarespace's IP addresses (e.g., 198.185.159.144). This step wouldn't have been possible if `ext-sq.squarespace.com` wasn't globally accessible.


### How is akjn.dev itself working?

The root domain `akjn.dev` (and `www.akjn.dev`) use a special **NETLIFY record type** in Netlify DNS.

| ![](/assets/images/netlify-NETLIFY-records.png) | 
|:--:| 
| *NETLIFY records in Netlify's DNS records* |

<br>

**Why NETLIFY record type?** DNS standards don't allow CNAME records on root domains (like `akjn.dev`). The NETLIFY record is Netlify's solution - it acts like a CNAME but actually creates A records behind the scenes.

**What actually happens:**
```bash
$ dig akjn.dev +short
13.215.239.219
52.74.6.109
# Returns A records (IP addresses), not CNAME!
```

Even though the UI shows it pointing to `akjn.netlify.com`, the actual DNS returns direct IP addresses.

**The akjn.netlify.com vs akjn.netlify.app Confusion**

Two different domains, two different purposes:

| Domain | Purpose | Works as Website? |
|--------|---------|-------------------|
| `akjn.netlify.com` | DNS endpoint for NETLIFY records | ❌ No - Returns 404 |
| `akjn.netlify.app` | Your actual project's website URL | ✅ Yes - Serves site |

<br>

**Why akjn.netlify.com exists but doesn't work as a website:**

```bash
# DNS resolution works
$ dig akjn.netlify.com +short
52.74.6.109
13.215.239.219

# But HTTP returns 404
$ curl -I https://akjn.netlify.com
HTTP/2 404
# Site Not Found!

# However, .app works
$ curl -I https://akjn.netlify.app
HTTP/2 200
# ✅ Works!
```

**Explanation:**
- `akjn.netlify.com` is a **DNS infrastructure endpoint** - it resolves to Netlify's servers but no project is mapped to this hostname
- `akjn.netlify.app` is your **actual project subdomain** - automatically created when you created the project, has your content
- Same IPs, different HTTP routing!

**The complete flow:**
```
NETLIFY record: akjn.dev → NETLIFY → akjn.netlify.com

Behind the scenes:
1. Netlify resolves akjn.netlify.com to current infrastructure IPs
2. Creates A records: akjn.dev → 13.215.239.219, 52.74.6.109
3. When user visits akjn.dev:
   → Connects to those IPs
   → HTTP Host header: akjn.dev
   → Netlify routes to your project
   → Serves website ✅

Note: akjn.netlify.app is never involved in this flow!
      It's just an alternate URL for the same project.
```

**Why both domains resolve to same IPs but behave differently:**

Virtual host routing! Netlify's servers check the HTTP `Host` header:
- `Host: akjn.dev` → Routes to your project ✅
- `Host: akjn.netlify.app` → Routes to your project ✅
- `Host: akjn.netlify.com` → No project mapped → 404 ❌

### Credits

This setup with my domain and subdomains would've been impossible without the help of <a href="https://x.com/AashutoshRathi" target="_blank">Aashutosh Senpai</a>. When I was setting up the domain long time ago (5 years ago, back in 2020 or something), he jumped on a call, guided me through the process, shared how his own domains and gazillion subdomains are setup, etc etc.

Since then, I've also asked SO MANY DOUBTS to him over the years on "how is this working", "why is this needed", "omg my website has been migrated to Squarespace what to do", and he has been super patient and always helpful.

This exploration and write-up was primarily for learning purposes, but also to hopefully be a good reference for my future-self if I ever get confused about these things again.
