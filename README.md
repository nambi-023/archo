# Counter — Conversational Checkout Agent

Built for the **Razorpay AI Buildathon — Track 01: AI Growth & Agentic Commerce**

## What it does

Counter is a chat-based checkout agent for an online storefront. Instead of
clicking through a catalog and cart, a customer just says what they want
("I want the blue hoodie, size M") and the agent:

1. Matches the request to live inventory
2. Confirms the exact product and price back to the customer before doing anything
3. Creates a Razorpay **test-mode** order
4. Handles an out-of-stock case by offering a real alternative instead of failing silently
5. Logs every one of these steps in a live **audit trail**, so no action the agent takes is unexplained

## Why an audit trail

The brief for this track asks for every money action to be explainable, bounded,
and gated, with at least one failure handled gracefully. The right-hand panel in
the demo is that requirement made visible: nothing the agent does — matching,
pricing, order creation, payment — happens without a corresponding log entry.

## Try it

Open `index.html` in a browser (no build step, no dependencies, no API key
needed to demo). Use the suggested chips or type your own request. Try
"blue hoodie size L" to see the out-of-stock → alternative flow.

## How it's built

- Single-page vanilla HTML/CSS/JS — no framework, no build tooling
- `parseIntent()` stands in for an LLM call: given a real API key, this function
  is replaced by a single call to an LLM that returns a structured
  `{ product_query, size, action }` object instead of the local keyword match
- Order creation is simulated to match the shape of Razorpay's test-mode
  Orders API response (`order_id`, amount, status) so swapping in the real
  API is a drop-in change, not a rewrite

## Architecture (current demo → production path)

```
User message
   │
   ▼
Intent parser  ──── demo: keyword match  /  prod: LLM API call
   │
   ▼
Inventory check ── match found? in stock?
   │                     │
   │                     └─ no → offer alternative or decline (gated stop)
   ▼
Price confirmation (shown to user before any charge)
   │
   ▼
Razorpay Orders API (test mode) ── order created
   │
   ▼
Payment capture ── logged to audit trail
```

## What's mocked vs real in this submission

- **Real:** UI/UX flow, inventory logic, audit trail, out-of-stock fallback logic
- **Mocked for demo:** the LLM call (replaced with local parsing) and the
  Razorpay API call (replaced with a simulated response shaped like the real
  one) — swapped for actual API calls is the next step, noted in the pitch video

## Challenges faced

See the buildathon submission form for the write-up on this — happy to expand
here too if useful for judges.
