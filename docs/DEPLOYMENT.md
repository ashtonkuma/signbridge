# Deployment Guide

SignBridge is a static web app — no server, no database, no build step.

## GitHub Pages (Free, Recommended)

1. Push your repository to GitHub
2. Go to **Settings → Pages**
3. Set Source: **Deploy from a branch** → **main** → **/ (root)**
4. Click Save — your app will be live at:
   ```
   https://your-username.github.io/signbridge/
   ```

The landing page (`index.html`) will load automatically. Phase links are relative and will work correctly.

## Netlify (Free)

### Drag & Drop
1. Zip the entire `signbridge/` folder
2. Go to [app.netlify.com](https://app.netlify.com)
3. Drag the zip onto the deploy area
4. Done — live in seconds with HTTPS

### Git Integration
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Deploy
netlify deploy --dir . --prod
```

## Vercel (Free)

```bash
npm install -g vercel
cd signbridge
vercel --prod
```

## Local Development

### Basic (no HTTPS)
```bash
# Python
python3 -m http.server 8080

# Node
npx serve .

# Open → http://localhost:8080
```

> ⚠️ Camera may not work at `http://` on some browsers. Use localhost (usually allowed) or add HTTPS.

### Local HTTPS with mkcert

```bash
# Install mkcert
brew install mkcert  # macOS
# or: https://github.com/FiloSottile/mkcert#installation

# Set up local CA
mkcert -install

# Generate cert for localhost
mkcert localhost

# Serve with HTTPS
npx serve . --ssl-cert localhost.pem --ssl-key localhost-key.pem

# Open → https://localhost:3000
```

### Using Vite (fastest dev server)

```bash
npm create vite@latest signbridge-dev -- --template vanilla
# Copy HTML files in, then:
npm run dev
```

## Camera Requirements

| Scenario | Camera Works? |
|----------|---------------|
| `https://` (any host) | ✅ Yes |
| `http://localhost` | ✅ Yes |
| `http://127.0.0.1` | ✅ Yes |
| `http://` (non-localhost) | ❌ No |
| File opened directly (`file://`) | ❌ No |

## Custom Domain

When hosted on GitHub Pages or Netlify, add a `CNAME` file to the repo root:
```
signbridge.yourdomain.com
```

Then configure your DNS provider to point that subdomain to your host's servers.

## Environment Notes

- **No environment variables needed** — everything is inline
- **No API keys** — MediaPipe loads from CDN, Web Speech API is browser-native
- **No database** — state is in-memory only; refreshing resets everything
- **CDN dependency** — MediaPipe loads from `cdn.jsdelivr.net`; an internet connection is required for the ML model on first load
