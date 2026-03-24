# Banking UI Upgrade Design

**Date:** 2026-03-24
**Status:** Approved

## Problem

The banking chatbot at `banking.alphonzojonesjr.com` reverted to its original basic purple-gradient chat UI because upgraded changes were applied directly to the cluster and ArgoCD's `selfHeal: true` reverted them. The upgrade needs to be committed to git so ArgoCD deploys and keeps it.

## Goal

Transform the basic chatbot into a full banking landing page matching the dark-theme design language of `chat.alphonzojonesjr.com`. Single HTML file, committed to the git repo's ConfigMap.

## Design

### Color System (matches chat site)
- Background: `#09090b` (base), `#0f0f13` (cards)
- Cards: Semi-transparent white overlays at 3-6% opacity
- Text: `#fafafa` (primary), `#a1a1aa` (secondary), `#52525b` (tertiary)
- Accents: green `#22c55e`, emerald `#10b981`, cyan `#06b6d4`, amber `#f59e0b`, rose `#f43f5e`

### Typography
- Body: Inter (Google Fonts CDN)
- Monospace: JetBrains Mono (Google Fonts CDN)
- Headings: Inter 800 weight, gradient text fill

### Sections (top to bottom)

1. **Nav bar** - Sticky. "SecureBank" logo left, status pills right (green pulsing "Live", "AI-Powered", "256-bit Encrypted"). Back link to `alphonzojonesjr.com`.

2. **Hero** - Centered. Monospace label "SECURE DIGITAL BANKING". Large gradient heading "Next-Gen Banking, Powered by AI". Subtitle. Fade-up animation.

3. **Capability badges** - Horizontal wrapped row of bordered pills with colored dots: Balance Check (green), Transfers (emerald), Bill Pay (cyan), ATM Finder (amber), Fraud Alerts (rose), Investments (purple).

4. **Stats grid** - 3-column grid with 1px gap separators: "99.9% Uptime" (green), "$2.4M+ Processed" (cyan), "24/7 AI Support" (amber). Monospace values, uppercase labels.

5. **Account cards** - 3 cards in responsive grid:
   - Checking ****1234: $5,432.10 (green accent)
   - Savings ****5678: $15,750.25 (emerald accent)
   - Credit ****9012: $2,340.50 available (cyan accent)
   - Each card: dark background, colored left accent, account type, masked number, balance

6. **Chat panel** - Dark panel with:
   - Header: green avatar, "SecureBank AI" title, green "Online" badge, clear button
   - Message area: bot bubbles (dark card style) and user bubbles (green-tinted gradient border)
   - Timestamps on messages
   - Typing indicator (bouncing green dots)
   - Quick action suggestions below input
   - Input: dark textarea + green gradient send button
   - Demo response logic: balance, transactions, transfers, ATM, help
   - AWS API Gateway fallback (existing endpoint)

7. **Ambient background** - 3 blurred orbs: green (top-right, 600px), emerald (bottom-left, 500px), cyan (center, 400px). `filter: blur(120px)`, low opacity, float animation.

### Responsive
- Mobile (<640px): pills hidden, account cards single column, reduced padding, message max-width 92%

### Animations
- Fade-up on load (staggered delays per section)
- Float animation on ambient orbs
- Pulse on "Live" indicator
- Bounce on typing dots
- Hover transitions on cards, badges, buttons

## Deployment

- Update HTML in ConfigMap within `apps/banking-chatbot/banking-app.yaml`
- Commit and push to `github.com/Newbigfonsz/banking-chatbot-k8s.git` main branch
- ArgoCD auto-syncs the change to the cluster
- No Dockerfile or image changes needed (nginx:alpine serves ConfigMap)

## Architecture (unchanged)

```
banking.alphonzojonesjr.com
    -> nginx Ingress
    -> banking-chatbot-svc (ClusterIP:80)
    -> nginx:alpine Pod (x2 replicas)
    -> /usr/share/nginx/html (ConfigMap volume)
    -> index.html (new landing page)
    -> POST AWS API Gateway (fallback to demo responses)
```
