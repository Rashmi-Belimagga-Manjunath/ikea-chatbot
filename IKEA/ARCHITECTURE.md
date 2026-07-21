# Architecture & Build Notes

## Project Structure

```
ikea-chatbot/
├── index.html               # Single-page application (everything in one file)
├── ikea_transaction_log.csv  # CSV export of all orders for Google Sheets import
├── IKEA/                     # Knowledge base (this folder)
│   ├── PRODUCT_CATALOGUE.md
│   ├── CUSTOMER_DATABASE.md
│   ├── ORDERS_DATABASE.md
│   ├── MCP_TOOLS.md
│   ├── API_INTEGRATION.md
│   ├── STORE_INFO.md
│   └── ARCHITECTURE.md (this file)
└── .gitignore
```

## Technology Stack

- **Runtime:** Vanilla JavaScript (no frameworks, no build tools)
- **UI:** HTML + CSS (single file, no preprocessors)
- **LLM Integration:** Direct fetch() to OpenAI and Gemini REST APIs
- **Deployment:** GitHub Pages (static hosting)
- **Storage:** browser localStorage (API keys, theme preference)

## Design System

### IKEA Brand Colours

| Token       | Hex       | Usage                          |
|-------------|-----------|--------------------------------|
| Blue        | `#0051ba` | Primary buttons, top bar, links |
| Blue-50     | `#eef4fb` | Card icons, light backgrounds   |
| Blue-600    | `#003d8c` | Button hover states             |
| Yellow      | `#ffcc00` | AI avatar, demo badge, secondary buttons |
| Green       | `#16a34a` | Delivered status, success       |
| Red         | `#dc2626` | Cancelled status, errors        |
| Orange      | `#ea580c` | Processing status               |

### Typography

- **Headings:** Playfair Display (serif) — premium, editorial feel
- **Body:** Inter (sans-serif) — clean, Scandinavian minimalism
- **Code/monospace:** SF Mono / Fira Code

### UI Components

- **Top bar:** Fixed height (44px), IKEA blue, contains logo, tagline, theme toggle, setup, reset
- **Sidebar:** 380px wide, results panel with product/order cards, spreadsheet view
- **Chat area:** Flexible width, message bubbles, suggestion chips, input bar
- **Modals:** Setup modal, overlay with backdrop blur
- **Cards:** Product cards with icon/name/price/tags, order cards with timeline/status

### Animations

- Spring-like cubic bezier: `cubic-bezier(0.34, 1.56, 0.64, 1)`
- Standard ease: `cubic-bezier(0.22, 1, 0.36, 1)`
- Message entrance: slide up + fade (`msgUp` keyframe)
- Card entrance: staggered card entrance (`cardUp` keyframe)
- Modal entrance: scale + slide up (`modalIn` keyframe)
- Typing indicator: bouncing dots
- Transitions on: background, border, box-shadow, transform

### Dark Mode

- Toggle via top bar button
- Persisted in localStorage under `ikea_dark`
- CSS custom properties switch via `.dark` class on body
- Smooth transition: `background .35s, color .35s`

## Data Architecture

All data is hardcoded in JavaScript (no external database):

- `PRODUCTS[30]` — Product catalogue
- `CUSTOMERS[30]` — Customer records
- `ORDERS[30]` — Order records (exactly one per customer)
- `STORE` — Store info object
- `OFFERS[6]` — IKEA Family offers array
- `TOOLS` — MCP tool definitions with async `fn` handlers

## Response Flow

```
User Input
    │
    ├── API key exists ──► OpenAI/Gemini with function calling
    │                         │
    │                         ├── LLM decides tool(s) + args
    │                         ├── exec() → returns dataset result
    │                         ├── Result sent back to LLM
    │                         ├── LLM generates natural language response
    │                         └── Sidebar rendered with result cards
    │
    └── Demo mode ──► Pattern-matching logic
                          │
                          ├── "store" → store_info tool
                          ├── "family"/"offer" → family_offers tool
                          ├── "category" → categories tool
                          ├── "order/track" → track_order / purchase_history
                          └── default → search_products
```

## CSV Export Format

The CSV has one row per product line item (not one row per order), creating 50+ rows total. Google Sheets compatible (UTF-8, quoted fields with comma escaping).

**Headers:** Order ID, Customer Name, Email, Phone, Order Date, Status, Item Name, Item ID, Qty, Unit Price (€), Line Total (€), Order Total (€), Delivery Address, County, Payment Method, Delivery Method, Courier, Tracking Number, Delivered Date, ETA Date, IKEA Family Points, IKEA Family Tier, Notes

## GitHub Integration

- **Repository:** https://github.com/Rashmi-Belimagga-Manjunath/ikea-chatbot
- **Deployment:** GitHub Pages at https://rashmi-belimagga-manjunath.github.io/ikea-chatbot/
- **Secret scanning:** GitHub blocks hardcoded `sk-*` API keys from being pushed
- **Security recommendation:** Use GitHub Actions with repo secrets for deployment, or keep keys in localStorage only

## Key Decisions

1. **Single HTML file** — No build step, no bundler, trivial to deploy on GitHub Pages
2. **Hardcoded dataset** — No external database needed, fully offline-capable
3. **One order per customer** — Unique names/emails for realistic lookup behaviour
4. **IKEA brand-first UI** — Designed as an internal IKEA tool for pitch-readiness
5. **Dual provider support** — OpenAI and Gemini with simple toggle
6. **Demo mode** — Works entirely offline without any API key
7. **Irish context** — Dublin store, Irish names/addresses/counties, EUR pricing
