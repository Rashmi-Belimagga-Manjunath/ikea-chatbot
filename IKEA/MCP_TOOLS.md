# IKEA MCP Tools

7 function-calling tools available to the LLM.

## Tool Definitions

### 1. search_products
Search IKEA products by name, category, or keyword.

- **Params:** `{ query: string }`
- **Returns:** `{ results: Product[], total: number, query: string }`
- **Example call:** `search_products({ query: "MALM bed" })`
- **Search logic:** Matches against name, category, tags, and description (case-insensitive). Falls back to word-level matching for short queries.

### 2. product_details
Get detailed information about a specific product.

- **Params:** `{ product: string }`
- **Returns:** `{ product: Product } | { error: string }`
- **Example call:** `product_details({ product: "MALM-001" })`
- **Match logic:** Exact ID match first, then partial name match.

### 3. track_order
Track an order by ID or find all orders for a customer email.

- **Params:** `{ orderId?: string, email?: string }`
- **Returns:** `{ order: Order } | { customer: Customer, orders: Order[] } | { error: string }`
- **Example calls:**
  - `track_order({ orderId: "IE-2024-011" })`
  - `track_order({ email: "aoife@example.com" })`

### 4. purchase_history
Get full purchase history and summary for a customer by email.

- **Params:** `{ email: string }`
- **Returns:** `{ customer: Customer, orders: Order[], summary: { total, spent, points } }`
- **Example call:** `purchase_history({ email: "aoife@example.com" })`

### 5. store_info
Get IKEA Ballymun store hours, services, facilities and directions.

- **Params:** None
- **Returns:** `{ store: Store }`
- **Store details:**
  - Name: IKEA Ballymun
  - Address: St. Margaret's Road, Ballymun, Dublin 11, D11 FW18
  - Phone: +353 (0)1 123 4567
  - Hours: Mon–Fri 10:00–21:00, Sat 10:00–20:00, Sun 11:00–18:00
  - Services: Swedish Restaurant, Småland Play Area, Click & Collect, Kitchen Planning, Bedroom Planning, Van Hire, Assembly Service, Personal Shopper
  - Facilities: Free parking (1000+), EV charging, Baby changing, Wheelchair accessible, Bike parking, Free WiFi

### 6. family_offers
Get current IKEA Family offers and member benefits.

- **Params:** None
- **Returns:** `{ offers: Offer[] }`
- **Current offers:**
  - Free tea or coffee (weekdays in restaurant)
  - Kids eat for €0.95
  - 4 for 3 on moving boxes (DUNDERGUBBE)
  - IKEA Family prices on selected products
  - Free workshops (DIY, planning, family events)
  - Earn points on every purchase

### 7. categories
List all product categories with product counts.

- **Params:** None
- **Returns:** `{ categories: { name: string, count: number }[] }`
- **Sorted by:** Count descending

*Note: `download_csv` and `view_transaction_log` tools removed to prevent public access to customer data.*

## Tool Execution Flow

1. User sends a natural language message
2. LLM decides which tool(s) to call and with what arguments
3. Tool executes against built-in dataset (no external API calls)
4. Result is returned to LLM for natural language response
5. Results also rendered in sidebar as product/order cards

## Dashboard

The sidebar **📊 Dashboard** button shows a business overview with:
- Total orders, revenue, average order value, customer count
- Orders broken down by status (delivered/shipped/processing/cancelled)
- Top 5 best-selling products
- Revenue by category
- Customer tier distribution (Gold/Silver/Standard)
