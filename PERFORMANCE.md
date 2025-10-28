# Performance Optimizations

This document describes the performance optimizations implemented in this project and recommended deployment practices.

## Implemented Optimizations

### 1. SVG Icon Extraction (55% HTML Size Reduction)
**Problem**: Inline SVG icons in the layout were duplicated in the HTML output for every page, adding ~3KB of overhead.

**Solution**: Extracted all social media icons (Instagram, GitHub, Email) to separate SVG files in `/public/icons/`. These are now:
- Cached separately by browsers
- Loaded only once and reused across pages
- Reduce HTML from 4.9KB to 2.1KB (55% reduction)

**Files**:
- `/public/icons/instagram.svg`
- `/public/icons/github.svg`
- `/public/icons/email.svg`

### 2. Build Optimizations
**Configured**:
- HTML compression enabled via `compressHTML: true`
- CSS minification using Lightning CSS
- JavaScript minification using Terser (when applicable)

**Configuration**: See `astro.config.mjs`

### 3. Removed Redundant CSS Classes
**Optimized**: Removed duplicate `max-w-7xl` class applications where nested elements already had the constraint.

### 4. Fixed Favicon MIME Type
**Changed**: `type="icon"` → `type="image/x-icon"` for proper browser handling and caching.

## Performance Metrics

### File Sizes (Uncompressed)
- HTML: 2.1 KB (down from 4.9 KB)
- CSS: 9.8 KB
- Icons: 3 KB total (cached separately)

### Gzipped Sizes (Expected over HTTP)
- HTML: ~1 KB
- CSS: ~2.8 KB
- Icons: ~1 KB total

## Recommended Deployment Settings

### 1. Enable Compression
Ensure your web server enables gzip or brotli compression for text files:

**Nginx example**:
```nginx
gzip on;
gzip_types text/html text/css application/javascript image/svg+xml;
gzip_min_length 1000;
```

**Cloudflare/Vercel/Netlify**: Compression is enabled by default.

### 2. Set Cache Headers

**For static assets** (`/_astro/*`, `/icons/*`, `/favicon.*`):
```
Cache-Control: public, max-age=31536000, immutable
```

**For HTML pages**:
```
Cache-Control: public, max-age=3600, must-revalidate
```

### 3. HTTP/2 or HTTP/3
Use HTTP/2 or HTTP/3 to enable multiplexing, which improves loading of multiple small assets (like icons).

### 4. CDN Usage
Consider using a CDN to serve static assets closer to users:
- Cloudflare
- AWS CloudFront
- Vercel Edge Network (default on Vercel)
- Netlify CDN (default on Netlify)

## Monitoring

### Core Web Vitals Targets
- **LCP** (Largest Contentful Paint): < 2.5s
- **FID** (First Input Delay): < 100ms
- **CLS** (Cumulative Layout Shift): < 0.1

### Tools
- [Google PageSpeed Insights](https://pagespeed.web.dev/)
- [WebPageTest](https://www.webpagetest.org/)
- Chrome DevTools Lighthouse

## Future Optimization Opportunities

1. **Image Optimization**: If images are added, use WebP/AVIF formats with proper sizing
2. **Font Loading**: If custom fonts are added, use `font-display: swap` and preload
3. **Code Splitting**: For multi-page applications, implement route-based code splitting
4. **Service Worker**: Consider adding for offline support and caching strategy
5. **Critical CSS**: For larger CSS files, inline critical CSS and defer non-critical styles

## Build Commands

```bash
# Development
npm run dev

# Production build
npm run build

# Preview production build
npm run preview
```

## Notes

- This is a static site, so there are no server-side rendering performance concerns
- The site is already very lightweight and loads quickly
- Most optimizations focus on reducing transfer sizes and improving caching
