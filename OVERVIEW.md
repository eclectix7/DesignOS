# Design OS - Application Overview

## Purpose

Design OS is a product planning and design tool that helps users define their product vision, structure their data model, design their UI, and export production-ready components for implementation. It provides a guided, step-by-step process for capturing product requirements and design specifications before implementation begins.

## Architecture

Design OS is a **client-side React application** built with:
- **React 19** with React Router for navigation
- **Vite** as the build tool and dev server
- **TypeScript** for type safety
- **Tailwind CSS** for styling
- **Radix UI** components for accessible UI primitives

The application operates entirely in the browser with **no backend server**. All data is loaded from local markdown files in the `/product/` directory using Vite's build-time file loading system (`import.meta.glob`).

## Application Flow

The application guides users through a structured design process:

1. **Product Planning** (`/`)
   - Define product vision, roadmap, and data model
   - Creates: `product/product-overview.md`, `product/product-roadmap.md`, `product/data-model/data-model.md`

2. **Design System** (`/design`)
   - Choose colors, typography, and design tokens
   - Creates: `product/design-system/colors.json`, `product/design-system/typography.json`

3. **Application Shell** (`/shell/design`)
   - Design the persistent navigation and layout wrapper
   - Creates: `product/shell/spec.md`, `src/shell/components/*.tsx`

4. **Section Design** (`/sections`, `/sections/:sectionId`)
   - Design individual feature sections and screens
   - Creates: `product/sections/[section-id]/spec.md`, `product/sections/[section-id]/data.json`, `src/sections/[section-id]/*.tsx`

5. **Export** (`/export`)
   - Generate a complete handoff package (`product-plan.zip`) for implementation
   - Includes prompts, instructions, components, types, and documentation

## Data Loading

All data is loaded from the local filesystem at **build time** using Vite's `import.meta.glob`:

- **Product files**: `/product/*.md` (product-overview.md, product-roadmap.md)
- **Data model**: `/product/data-model/*.md`
- **Design system**: `/product/design-system/*.json`
- **Shell specs**: `/product/shell/*.md`
- **Section specs**: `/product/sections/*/spec.md`
- **Section data**: `/product/sections/*/data.json`
- **Section screenshots**: `/product/sections/*/*.png`
- **Screen design components**: `/src/sections/*/*.tsx` (lazy loaded)
- **Export zip**: `/product-plan.zip` (if generated)

No runtime file system access or API calls are made. All data is bundled into the application at build time.

## External Data Transmission

### Outbound Network Requests

**1. Google Fonts API** (Browser-initiated, not application code)
- **URL**: `https://fonts.googleapis.com/css2?family=DM+Sans:...&family=IBM+Plex+Mono:...`
- **Purpose**: Load web fonts for the application UI
- **Data Sent**:
  - Font family names and weights requested
  - Browser user agent string (standard HTTP header)
  - Referrer (if configured)
- **Location**: `index.html` (lines 7-9)
- **Type**: Standard browser font loading via `<link>` tag

**2. Google Fonts CDN** (Browser-initiated, not application code)
- **URL**: `https://fonts.gstatic.com/*`
- **Purpose**: Download actual font files (WOFF2, etc.)
- **Data Sent**:
  - Browser user agent string
  - Referrer header
- **Location**: Triggered by the Google Fonts CSS link in `index.html`
- **Type**: Standard browser resource loading

**3. External Link** (User-initiated navigation only)
- **URL**: `https://buildermethods.com/design-os`
- **Purpose**: Footer link to Design OS website
- **Data Sent**: None (standard browser navigation)
- **Location**: `src/components/AppLayout.tsx` (line 93)
- **Type**: Standard anchor tag with `target="_blank"` and `rel="noopener noreferrer"`

### No Application-Initiated Network Requests

The application code does **not** make any:
- `fetch()` calls
- `XMLHttpRequest` calls
- WebSocket connections
- `curl` commands or server-side HTTP requests
- Analytics or tracking requests
- API calls to external services
- Form submissions to external endpoints

### Export Functionality

The export feature generates a `product-plan.zip` file that is:
- Created locally (via Claude Code commands, not in the application code)
- Downloaded by the user via a standard browser download (`<a href>` with `download` attribute)
- **Not uploaded or transmitted anywhere** - it's purely a local file download

## Data Privacy & Security

- **No user data collection**: The application does not collect, store, or transmit any user data
- **No analytics**: No tracking scripts or analytics services are integrated
- **No authentication**: No user accounts or authentication system
- **Local-only operation**: All work is stored in local markdown files in the project directory
- **No telemetry**: No error reporting or usage telemetry sent to external services

## Browser Requirements

The application requires:
- Modern browser with ES modules support
- JavaScript enabled
- Network access to Google Fonts (for font loading)

## Build & Deployment

- **Development**: `npm run dev` - Starts Vite dev server on port 3000
- **Production**: `npm run build` - Builds static assets to `dist/` directory
- **Deployment**: Can be deployed as static files to any web server (no server-side requirements)

## Summary

Design OS is a **completely self-contained, client-side application** that operates entirely within the browser. The only external network activity is:
1. Browser-initiated font loading from Google Fonts (standard web font loading)
2. User-initiated navigation to external website (footer link)

No application data, user information, or product designs are transmitted to any external service. All work remains local to the user's project directory.
