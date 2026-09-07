# Demystifying DNS: From URL to Browser Response

**Author:** Shikhar Singh  
**Track:** General AI Fluency (Assignment Code: `PF-04`)  
**Topic:** How Web Infrastructure Works — Resolvers, Nameservers, Records, and CNAMEs  

---

## 1. What Does DNS Actually Do?

Computers that power the internet do not understand human-friendly names like `shikhar3dev.github.io` or `google.com`. They communicate using numeric **IP (Internet Protocol) addresses**, such as `185.199.108.153` (IPv4) or `2606:4700::6811:d209` (IPv6).

The **Domain Name System (DNS)** is the internet's decentralized phonebook. Just as your smartphone lets you tap a friend's name instead of memorizing their 10-digit phone number, DNS transparently translates human-readable domain names into machine-routable IP addresses every time you visit a website.

---

## 2. What Happens When You Type a Website Address?

Imagine you want to mail a letter to someone, but you only know their full name, not their street address. You ask a courier to find the exact street coordinates. 

Here is the exact step-by-step relay race that happens in under 50 milliseconds when someone types `https://shikhar3dev.github.io/ai-portfolio/` into their browser:

```
[User Browser] 
      │ 
      ▼
1. Browser & OS Cache  ─── (Cache Miss) ───► 2. Recursive Resolver (ISP / 1.1.1.1)
                                                      │
                                                      ├─► 3. Root Nameserver (.)
                                                      │        └─ "Go ask the .io TLD server"
                                                      │
                                                      ├─► 4. TLD Nameserver (.io)
                                                      │        └─ "Go ask GitHub's Authoritative Nameserver"
                                                      │
                                                      └─► 5. Authoritative Nameserver (ns1.github.com)
                                                               └─ "Here is the exact IP: 185.199.108.153!"
      │
      ▼
6. Browser connects to 185.199.108.153 via HTTPS ───► Page Renders!
```

### Step 1: Local Cache Check
Before touching the internet, your browser checks its own local memory, followed by your computer's operating system cache. If you visited the site 5 minutes ago, your computer already knows the IP address and connects immediately.

### Step 2: The Recursive Resolver (Your Personal Investigator)
If the address isn't cached locally, your computer contacts a **Recursive DNS Resolver** (typically provided by your Internet Service Provider or a public DNS service like Cloudflare `1.1.1.1` or Google `8.8.8.8`). The resolver's sole job is to track down the IP address across the global hierarchy.

### Step 3: The Root Nameserver (`.`)
The resolver first queries a **Root Nameserver**. There are 13 logical root server clusters distributed across thousands of physical locations worldwide. The root server doesn't know the exact IP, but it reads the top-level domain suffix (`.io`) and replies:  
*"I don't have that specific record, but here is the address of the `.io` Top-Level Domain (TLD) nameserver."*

### Step 4: The TLD Nameserver (`.io`)
The resolver then contacts the **TLD Nameserver** for `.io`. The TLD server inspects the domain (`github.io`) and points to the entity with final authority:  
*"I don't know the specific page, but here are the Authoritative Nameservers managed by GitHub Pages."*

### Step 5: The Authoritative Nameserver (The Final Truth)
Finally, the resolver queries the **Authoritative Nameserver** (e.g., `ns1.github.com`). This server holds the official DNS zone records for the domain. It looks up the record for `shikhar3dev.github.io` and returns the destination IP address: `185.199.108.153`.

### Step 6: Response, Caching, and TLS Handshake
The resolver hands this IP back to your browser, storing it in memory for a specified duration called the **TTL (Time to Live)** so future requests are instant. Your browser then establishes an encrypted **HTTPS connection (TCP + TLS handshake)** directly with the web server at `185.199.108.153`, requesting the HTML page and rendering it on your screen.

---

## 3. What is a CNAME Record (and How Does it Differ from an A Record)?

When configuring a website, you encounter two primary types of DNS address records:

| Record Type | What it Stands For | How it Works | Real-World Analogy |
| :--- | :--- | :--- | :--- |
| **A Record** | Address Record | Maps a hostname **directly to an exact IP address** (e.g., `shikharsingh.com` $\rightarrow$ `185.199.108.153`). | Giving someone your exact GPS latitude and longitude coordinates. |
| **CNAME Record** | Canonical Name Record | Maps an **alias name to another domain name**, creating a pointer (e.g., `www.shikharsingh.com` $\rightarrow$ `shikhar3dev.github.io`). | Forwarding your postal mail from your old nickname to your new official residence. |

### Why Modern Hosts (GitHub Pages, Netlify, Vercel) Rely on CNAMEs
Cloud hosting platforms manage thousands of edge servers worldwide behind Content Delivery Networks (CDNs). If GitHub Pages or Netlify needs to upgrade their server fleet or re-route traffic during a DDoS attack, their physical IP addresses may change. 

If you used a hardcoded **A record**, your website would go down whenever their IPs changed. By using a **CNAME record**, your custom domain points to their hostname (`shikhar3dev.github.io`). When their underlying infrastructure shifts, GitHub updates its own DNS records, and your custom domain continues working seamlessly with zero downtime or manual re-configuration.

---

## 4. Why Automatic HTTPS Matters

Notice the `https://` prefix and the padlock icon in the browser address bar. Modern web hosts (GitHub Pages, Netlify) automatically provision free SSL/TLS certificates via **Let's Encrypt**. 

This ensures:
1. **Encryption in Transit:** No third party on the public Wi-Fi network can eavesdrop on or tamper with data passing between the visitor and your portfolio.
2. **Identity Verification:** The certificate cryptographically proves that the server serving the website is the legitimate owner of `shikhar3dev.github.io`.
3. **SEO & Trust:** Modern browsers flag unencrypted `http://` sites as "Not Secure," and search engines penalize non-HTTPS domains in search rankings.
