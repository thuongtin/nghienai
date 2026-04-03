---
title: "feat: nghien.ai Domain For Sale Landing Page"
type: feat
status: active
date: 2026-03-24
---

# nghien.ai -- Domain For Sale Landing Page

## Overview

Tạo static landing page tối giản, chuyên nghiệp trên **nghien.ai** để thông báo domain đang được bán. Deploy miễn phí trên **Cloudflare Pages free tier**. Không cần backend, database, hay framework.

Domain "nghien.ai" có nghĩa tiếng Việt là "Nghiện AI" (Addicted to AI) -- đây là selling point cần highlight cho international buyers.

## Problem Statement / Motivation

- Domain nghien.ai đang trỏ về Cloudflare nhưng chưa có nội dung
- Cần landing page để mọi visitor biết domain đang bán và có cách liên hệ
- Phải hoàn toàn miễn phí (Cloudflare Pages free tier)
- Phải professional, tải nhanh, mobile-friendly

## Proposed Solution

Single-page static website (HTML + CSS + minimal vanilla JS) deploy trên Cloudflare Pages.

### Tech Stack

| Component | Choice | Lý do |
|-----------|--------|-------|
| Hosting | Cloudflare Pages (free) | Unlimited bandwidth, free SSL, free custom domain |
| Frontend | Pure HTML + CSS | Zero dependencies, instant load, < 50KB |
| JS | Vanilla JS (minimal) | Chỉ dùng cho email obfuscation |
| Deploy | Wrangler CLI | Scriptable, repeatable |
| Analytics | Cloudflare Web Analytics | Free, no JS required, bật trong dashboard |

### Cloudflare Pages Free Tier Confirmation

| Feature | Free Limit | Đủ dùng? |
|---------|-----------|----------|
| Projects | 100/account | Yes |
| Builds/month | 500 | Yes |
| Bandwidth | **Unlimited** | Yes |
| Custom domains | 100/project | Yes |
| SSL | Auto, free | Yes |

**Kết luận: Cloudflare Pages free tier hoàn toàn đủ.**

## Design Specification

### Layout: Ultra-Minimalist, Single Viewport

```
+-----------------------------------------------+
|                                                 |
|              nghien .ai                         |
|        This premium domain is for sale          |
|                                                 |
|   "Nghien" = "Addicted" in Vietnamese           |
|        nghien.ai = Addicted to AI               |
|                                                 |
|           [ Make an Offer ]                     |
|                                                 |
|     contact@nghien.ai (JS-assembled)            |
|                                                 |
+-----------------------------------------------+
```

### Design Tokens

- **Background:** Dark navy/near-black (`#0a0a1a` hoặc tương tự)
- **Text:** Light/white (`#f0f0f0`)
- **Accent:** Electric blue hoặc gradient cho ".ai" và CTA button
- **Font:** System font stack (zero external dependency, fastest load)
  - Alternative: 1 Google Font (Inter/Space Grotesk) loaded async cho domain name
- **Domain name size:** 48-72px desktop, scale responsive
- **CTA button:** High contrast, rounded corners, hover effect

### Mobile Responsive

- Single column layout, tự nhiên responsive
- Font sizes scale down cho mobile
- CTA button full-width trên mobile
- Touch target >= 48px

## File Structure

```
nghienai/
  index.html        # Single page, tất cả content
  style.css         # Separated styles (cacheable)
  _headers          # Security headers (X-Frame-Options, CSP)
  _redirects        # www -> apex redirect (nếu cần)
  404.html          # Custom 404 redirect về trang chính
  favicon.svg       # Simple favicon (chữ "N" hoặc AI icon)
  og-image.png      # Social share image (1200x630px)
```

## Technical Considerations

### Email Anti-Spam (JS Obfuscation)

```javascript
// Email assembled at runtime -- bots không harvest được
const u = 'contact';
const d = 'nghien.ai';
const el = document.getElementById('email-link');
el.href = 'mailto:' + u + '@' + d;
el.textContent = u + '@' + d;
```

### JS-Disabled Fallback (Critical)

```html
<noscript>
  <p>Email: contact [at] nghien [dot] ai</p>
</noscript>
```

### SEO Meta Tags

```html
<title>nghien.ai - Premium AI Domain For Sale</title>
<meta name="description" content="nghien.ai is a premium .ai domain available
  for purchase. 'Nghien' means 'addicted' in Vietnamese. Perfect for AI-focused
  businesses and platforms.">
<meta property="og:title" content="nghien.ai - Premium Domain For Sale">
<meta property="og:description" content="Premium .ai domain available.
  Nghien = Addicted to AI.">
<meta property="og:image" content="https://nghien.ai/og-image.png">
<meta name="twitter:card" content="summary_large_image">
```

### Structured Data (JSON-LD)

```json
{
  "@context": "https://schema.org",
  "@type": "WebPage",
  "name": "nghien.ai - Domain For Sale",
  "description": "Premium .ai domain for sale",
  "url": "https://nghien.ai/",
  "offers": {
    "@type": "Offer",
    "itemOffered": {
      "@type": "Product",
      "name": "nghien.ai",
      "category": "Domain Name"
    }
  }
}
```

### Security Headers (`_headers` file)

```
/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Permissions-Policy: camera=(), microphone=(), geolocation=()
```

### Cloudflare Configuration (Dashboard)

- [ ] Bật "Always Use HTTPS"
- [ ] Tạo redirect rule: `www.nghien.ai` -> `nghien.ai` (301)
- [ ] Bật Cloudflare Web Analytics (free, zero code)
- [ ] Verify custom domain binding cho Pages project

## Acceptance Criteria

### Functional

- [ ] Truy cập `https://nghien.ai` hiển thị landing page đúng design
- [ ] Domain name "nghien.ai" hiển thị nổi bật, phần ".ai" được highlight
- [ ] Thông báo "This premium domain is for sale" rõ ràng
- [ ] Giải thích ý nghĩa tiếng Việt: "Nghien = Addicted to AI"
- [ ] CTA "Make an Offer" button link đến `mailto:` (assembled by JS)
- [ ] Email hiển thị đúng khi JS enabled
- [ ] `<noscript>` fallback hiển thị email dạng obfuscated khi JS disabled
- [ ] `www.nghien.ai` redirect 301 về `nghien.ai`
- [ ] `nghien.ai/random-path` hiển thị custom 404 hoặc redirect về index
- [ ] Favicon hiển thị trên browser tab

### Non-Functional

- [ ] Page load < 1 giây (target < 500ms cho static)
- [ ] Total page weight < 50KB (excluding OG image)
- [ ] Mobile responsive -- hiển thị tốt trên 320px -> 1440px+
- [ ] Lighthouse Performance score >= 95
- [ ] HTTPS enforced
- [ ] OG preview hiển thị đúng trên Facebook/Twitter sharing debugger

## Implementation Phases

### Phase 1: Core Page (MVP)

1. Tạo `index.html` + `style.css` với layout minimalist
2. Email obfuscation bằng JS + noscript fallback
3. SEO meta tags + OG tags
4. Favicon
5. Test locally

### Phase 2: Deploy & Config

1. Tạo Cloudflare Pages project via wrangler CLI
2. Deploy static files
3. Bind custom domain `nghien.ai`
4. Config: HTTPS enforce, www redirect, Web Analytics
5. Verify live site

### Phase 3: Polish

1. Tạo OG image (1200x630px)
2. `_headers` file cho security headers
3. `404.html` custom page
4. Test OG preview, mobile, JS-disabled, 404 handling
5. Lighthouse audit

## Deploy Commands

```bash
# Install wrangler (nếu chưa có)
npm install -g wrangler

# Login Cloudflare
wrangler login

# Tạo project
wrangler pages project create nghienai

# Deploy
wrangler pages deploy . --project-name=nghienai
```

## Success Metrics

- Landing page live tại `https://nghien.ai`
- Lighthouse >= 95 across all categories
- OG preview hiển thị chính xác
- Email liên hệ hoạt động (test gửi mail)
- Cloudflare Web Analytics tracking visitors

## Dependencies & Risks

| Risk | Mitigation |
|------|-----------|
| Domain DNS chưa trỏ đúng | Verify nameservers trỏ về Cloudflare |
| Email bị spam filter | Dùng domain email (contact@nghien.ai) thay vì personal |
| Page quá đơn giản, thiếu trust | Design professional, HTTPS, security headers |
| Không ai thấy trang | OG tags cho social sharing, structured data cho SEO |

## Sources & References

- [Cloudflare Pages Limits](https://developers.cloudflare.com/pages/platform/limits/)
- [Cloudflare Pages Deploy Guide](https://developers.cloudflare.com/pages/framework-guides/deploy-anything/)
- [Cloudflare Pages Custom Domains](https://developers.cloudflare.com/pages/configuration/custom-domains/)
- [Cloudflare Web Analytics](https://developers.cloudflare.com/analytics/web-analytics/)
- [Email Obfuscation Best Practices](https://spencermortensen.com/articles/email-obfuscation/)
- [Domain For Sale Page Patterns - Afternic/Dan.com/Sedo templates]
