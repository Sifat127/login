# PaperFlicker

**Paste a paper DOI — get the PDF instantly.**

PaperFlicker is a sleek, modern web application that converts academic paper DOIs into downloadable PDFs. Built for researchers, students, and anyone who needs quick access to IEEE and other academic papers.

![React](https://img.shields.io/badge/React-19-blue?logo=react)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9-blue?logo=typescript)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4.1-38bdf8?logo=tailwindcss)
![Vite](https://img.shields.io/badge/Vite-7.3-646cff?logo=vite)

---

## Features

### Authentication
- **Supabase Auth** — Full email/password sign up and login
- **Protected access** — Only authenticated users can access the tool
- **User menu** — Avatar initial, name, email display, sign out
- **Dev mode** — Runs without auth when Supabase is not configured

### Core Workflow
- **DOI-first input** — Paste any DOI (e.g. `10.1109/TVT.2018.2884767`) and get the PDF link in seconds
- **IEEE Xplore URL support** — Also accepts full IEEE Xplore URLs and `doi.org` links
- **Automatic metadata enrichment** — Fetches paper title, authors, and abstract from Semantic Scholar and CrossRef APIs
- **Sci-Hub integration** — Generates direct download links via `sci-hub.sidesgame.com` with 4 alternative mirror links
- **DOI sanitization** — Automatically strips leading colons, slashes, and whitespace from DOI values

### Metadata Resolution Pipeline
1. **DOI path** — Queries Semantic Scholar Graph API → CrossRef fallback
2. **IEEE URL path** — Fetches IEEE page via CORS proxies (allorigins.win, corsproxy.io) → Parses embedded `xplGlobal.document.metadata` JSON → CrossRef fallback → Semantic Scholar enrichment
3. **Manual DOI entry** — Error screen offers direct DOI input as a fallback when automatic extraction fails

### User Interface
- **Dark mode glassmorphism** — Layered glass cards with backdrop blur, subtle borders, and depth shadows
- **Neo light effects** — Animated aurora sweeps, floating orbs, scanline overlay, pulsing glow borders
- **Animated rotating gradient border** on the input field when focused
- **4-step workflow progress** with animated progress bar, step-by-step status indicators, and spring animations
- **Copy buttons** for DOI and PDF URL with clipboard feedback
- **JSON response viewer** — Expandable raw JSON output with copy support
- **Alternative mirror links** — 5 Sci-Hub mirrors in a toggleable panel
- **Recent paper history** — Last 10 papers persisted in localStorage
- **Toast notifications** — Success, error, warning, and info toasts with spring animations
- **Live input validation** — Real-time hint as you type
- **Step-by-step DOI guide** — Visual timeline tutorial showing how to find a DOI on IEEE Xplore
- **Fully responsive** — Optimized for mobile, tablet, and desktop

### Error Handling
- Invalid URL/DOI detection with specific error messages
- DOI not found with troubleshooting tips
- Network error recovery with retry button
- Manual DOI entry fallback on extraction failure
- Multiple CORS proxy fallthrough (tries 3 proxies sequentially)
- Request timeouts with AbortController (10-15s per request)

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | React 19 |
| Language | TypeScript 5.9 |
| Build Tool | Vite 7.3 |
| Styling | Tailwind CSS 4.1 |
| Animations | Framer Motion |
| Icons | Lucide React + custom SVG (GitHub, LinkedIn) |
| Fonts | Outfit (brand), Plus Jakarta Sans (body), Space Grotesk (headings), JetBrains Mono (code) |
| Auth | Supabase (email/password) |
| Output | Single HTML file (vite-plugin-singlefile) |

### External APIs Used
- **Supabase Auth** — User authentication (sign up, login, session management)
- **Semantic Scholar Graph API** — Paper metadata by DOI (title, authors, abstract)
- **CrossRef REST API** — Fallback metadata resolution
- **AllOrigins / CorsProxy.io** — CORS proxy for fetching IEEE Xplore pages
- **Sci-Hub (sidesgame.com)** — PDF source

---

## Project Structure

```
src/
├── App.tsx                          # Root — wraps AuthProvider, routes login vs dashboard
├── main.tsx                         # React entry point
├── vite-env.d.ts                    # Vite environment variable types
├── index.css                        # Tailwind imports, theme variables, custom animations
├── types.ts                         # TypeScript interfaces, workflow steps, error messages
├── helpers.ts                       # DOI validation, CORS proxy fetching, metadata extraction,
│                                    #   IEEE HTML parsing, Sci-Hub URL building, clipboard utils
├── lib/
│   ├── supabase.ts                  # Supabase client initialization
│   └── AuthContext.tsx              # React context for auth state, signUp/signIn/signOut
├── utils/
│   └── cn.ts                        # Tailwind class merge utility
└── components/
    ├── AuthPage.tsx                 # Login / Sign Up form with tabs
    ├── LoadingScreen.tsx            # Spinner shown during auth check
    ├── UserMenu.tsx                 # Top-right avatar dropdown with sign out
    ├── Dashboard.tsx                # Main app (DOI input, results, history, guide)
    ├── Header.tsx                   # Logo, subtitle, pipeline tag
    ├── UrlInput.tsx                 # DOI input with focus glow, paste button, validation hints
    ├── WorkflowProgress.tsx         # 4-step animated progress with status indicators
    ├── ResultCard.tsx               # Paper details, copy buttons, download/preview, mirrors, JSON
    ├── ErrorDisplay.tsx             # Error messages, retry, manual DOI entry
    ├── DoiGuide.tsx                 # 6-step visual timeline guide for finding DOIs
    ├── RecentHistory.tsx            # localStorage-persisted paper history
    ├── Toast.tsx                    # Toast notification system
    ├── BackgroundEffects.tsx        # Floating orbs, grid, scanline (mobile-optimized)
    └── Footer.tsx                   # Credits, social links (GitHub, LinkedIn), version
```

---

## Getting Started

### Prerequisites
- Node.js 18+
- npm 9+

### Installation

```bash
git clone https://github.com/Sifat127/paperflicker.git
cd paperflicker
npm install
```

### Supabase Setup (Authentication)

1. Go to [supabase.com](https://supabase.com) → Create a free account
2. Click **"New Project"** → Name it `paperflicker`
3. Wait for the project to finish provisioning (~30 seconds)
4. Go to **Settings → API** and copy:
   - **Project URL** (e.g. `https://abcdef.supabase.co`)
   - **anon public key** (starts with `eyJ...`)
5. Create a `.env` file in the project root:

```bash
VITE_SUPABASE_URL=https://your-project-id.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-here
```

6. In your Supabase dashboard, go to **Authentication → Settings**:
   - Under **Email Auth**, make sure "Enable Email Signup" is ON
   - Optionally disable "Confirm Email" for faster testing

> **Note:** If `.env` is not configured, the app runs in **open mode** (no login required) for development.

### Development

```bash
npm run dev
```

Opens at `http://localhost:5173`

### Production Build

```bash
npm run build
```

Outputs a single self-contained `dist/index.html` file (~440KB) with all JS, CSS, and assets inlined.

### Preview Build

```bash
npm run preview
```

---

## Deployment

The build output is a **single HTML file** — deploy it anywhere that serves static files.

### Netlify (Recommended)
1. Push to GitHub
2. Go to [app.netlify.com](https://app.netlify.com) → New site from Git
3. Build command: `npm run build`
4. Publish directory: `dist`
5. Deploy

**Or drag-and-drop:** Go to [app.netlify.com/drop](https://app.netlify.com/drop) and drop the `dist` folder.

### Vercel
1. Push to GitHub
2. Import at [vercel.com](https://vercel.com)
3. Auto-detected as Vite — click Deploy

### GitHub Pages
```bash
npm install -D gh-pages
```
Add to `package.json` scripts:
```json
"deploy": "npm run build && gh-pages -d dist"
```
Add `base: '/paperflicker/'` to `vite.config.ts`, then:
```bash
npm run deploy
```

---

## How It Works

### DOI Input Path (Primary)
1. User pastes a DOI like `10.1109/TVT.2018.2884767`
2. Input is validated and sanitized (strips `:`, `/`, whitespace)
3. Semantic Scholar API is queried for title, authors, and abstract
4. If S2 fails, CrossRef API is used as fallback
5. Sci-Hub URL is constructed: `https://sci-hub.sidesgame.com/{DOI}`
6. Result card shows paper details with download link

### IEEE URL Path (Secondary)
1. User pastes an IEEE Xplore URL
2. Document ID is extracted from the URL via regex
3. IEEE page is fetched through CORS proxy (allorigins.win)
4. Embedded `xplGlobal.document.metadata` JSON is parsed using a balanced-brace extractor that handles nested objects and escaped strings
5. DOI, title, authors, and abstract are extracted from the metadata
6. If proxy fails, CrossRef is tried as fallback
7. Missing metadata is enriched via Semantic Scholar API

### DOI Sanitization
The `cleanDoi()` function strips leading colons, slashes, and whitespace from DOI values. This prevents malformed URLs like `https://sci-hub.sidesgame.com/:10.1109/...` — ensuring clean URLs like `https://sci-hub.sidesgame.com/10.1109/...`.

---

## Accepted Input Formats

| Format | Example |
|--------|---------|
| Plain DOI | `10.1109/TVT.2018.2884767` |
| DOI with prefix | `doi:10.1109/TVT.2018.2884767` |
| doi.org URL | `https://doi.org/10.1109/TVT.2018.2884767` |
| IEEE Xplore URL | `https://ieeexplore.ieee.org/document/8556480` |
| IEEE abstract URL | `https://ieeexplore.ieee.org/abstract/document/8556480` |
| IEEE stamp URL | `https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8556480` |

---

## Design System

### Colors
- Background: `#030014` (deep space)
- Primary accent: Violet (`#8b5cf6`) → Fuchsia (`#d946ef`) → Pink (`#ec4899`)
- Success: Emerald (`#10b981`)
- Error: Red (`#ef4444`)
- Glass surfaces: `rgba(12, 12, 30, 0.65)` with `blur(24px)`

### Typography
- **Outfit** — Brand name (`font-brand`), extra-bold, geometric
- **Plus Jakarta Sans** — Body text (`font-sans`), clean and readable
- **Space Grotesk** — Section headings (`font-display`), technical feel
- **JetBrains Mono** — DOIs, URLs, code (`font-mono`), developer aesthetic

### Animation Principles
- Entry animations use `[0.22, 1, 0.36, 1]` easing (smooth deceleration)
- Spring physics for interactive elements (stiffness: 80-300)
- Staggered reveals with 80-120ms delays between siblings
- Gradient shimmer on brand text (4s cycle)
- Floating orbs at 22-28s cycles for ambient motion

---

## Developer

**A.S. Sifat Ahmed** — CSE, Daffodil International University (DIU)

- GitHub: [github.com/Sifat127](https://github.com/Sifat127)
- LinkedIn: [linkedin.com/in/a-s-sifat-ahmed-90a131315](https://www.linkedin.com/in/a-s-sifat-ahmed-90a131315/)

---

## License

This project is open source and available under the [MIT License](LICENSE).
