# Expose Your Local Server to the Internet for Free (Cloudflare Tunnel)

This guide explains how to expose a **local server (API, website, or SSH)** running on your machine or Android phone (via Termux) to the public internet **for free**, without:

- Buying a domain
- Opening router ports
- Configuring NAT or static IPs

We use **Cloudflare Tunnel (trycloudflare)**, which works over outbound connections and is ideal for development, demos, and personal projects.

## What is Cloudflare Tunnel?

Cloudflare Tunnel creates a secure outbound connection from your local machine to Cloudflare’s edge network. Cloudflare then provides a public HTTPS URL that forwards traffic to your local service.

Key benefits:
- No domain required
- Works behind NAT / mobile networks
- Free to use (quick tunnels)
- HTTPS enabled by default

## Prerequisites

- Linux / macOS / Windows or Android (Termux)
- A local server running (HTTP or SSH)
- `cloudflared` installed

---

## Install cloudflared

### Linux / macOS
```bash
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 \
  -o cloudflared
chmod +x cloudflared
sudo mv cloudflared /usr/local/bin/
````

### Termux (Android)

```bash
pkg update
pkg install cloudflared
```

Verify installation:

```bash
cloudflared --version
```

---

## Example 1: Expose a Local HTTP Server (API / Website)

### Step 1: Run a local server (In Termux on phone)

Example using Python ( see readme.md for server.py code):

```bash
python server.py
```

The server is now running on:

```
http://localhost:8000
```

---

### Step 2: Create a free Cloudflare tunnel

```bash
cloudflared tunnel --url http://localhost:8000
```

Cloudflare will generate a public URL like:

```
https://random-name.trycloudflare.com
```

---

### Step 3: Access from anywhere

```bash
curl https://random-name.trycloudflare.com
```

You can also open the URL in a browser.

---

## Example 2: Expose SSH (Remote Access)

### Step 1: Ensure SSH is running

On Termux, SSH usually runs on port `8022`.

Check:

```bash
ss -tlnp | grep 8022
```

---

### Step 2: Create an SSH tunnel

```bash
cloudflared tunnel --url ssh://localhost:8022
```

This creates a public hostname on `trycloudflare.com`.

---

### Step 3: Connect from your laptop

```bash
ssh -o ProxyCommand="cloudflared access ssh --hostname <hostname>" user@<hostname>
```

Replace `<hostname>` with the generated `trycloudflare.com` URL.

---

## Important Notes

* Quick tunnels (`trycloudflare.com`) are free but **temporary**
* URLs change every time you restart the tunnel
* Only one service is exposed per tunnel
* For production or persistent URLs, use a named tunnel with a Cloudflare account

---

## Common Use Cases

* Hosting APIs from a phone or laptop
* Remote SSH access without port forwarding
* Sharing demos or prototypes
* Testing webhooks locally
* Turning an Android phone into a lightweight server

---

## Limitations

* No uptime guarantee for free tunnels
* URL changes on restart
* Not intended for heavy production traffic


## Summary

With Cloudflare Tunnel, you can securely expose local services to the internet in minutes, completely free, without networking complexity. This is one of the simplest ways to turn any device into a remotely accessible server.
