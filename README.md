# AutoPartsHub — Car Spare Parts Search & Service System

> A full-featured web application for searching, selecting, and purchasing genuine car spare parts. Built with **React.js**, **HTML**, **CSS**, and **JavaScript**.
>
> **Team Members:**
> | S.No | Reg. No | Name |
> |------|------------|------|
> | 1 | 24BAI1067 | Brian Daniel Bennett |
> | 2 | 24BAI1075 | Amisha Balaji Krishnan |
> | 3 | 24BAI1341 | Cedric Antony Joseph |
> | 4 | 24BAI1612 | Shaasvat Rajagopalan |

---

## Table of Contents
1. [Project Overview](#1-project-overview)
2. [Tech Stack](#2-tech-stack)
3. [Project Structure](#3-project-structure)
4. [Architecture & Data Flow](#4-architecture--data-flow)
5. [Pages & Components — Detailed Breakdown](#5-pages--components--detailed-breakdown)
   - [App.js — Root Router](#51-appjs--root-router)
   - [Navbar](#52-navbar-component)
   - [Home — Vehicle Search](#53-home-page--vehicle-search)
   - [Parts — Catalogue](#54-parts-page--catalogue)
   - [PartCard — Product Card](#55-partcard-component)
   - [Cart](#56-cart-page)
   - [Payment](#57-payment-page)
   - [Receipt](#58-receipt-page)
   - [DIY Guides](#59-diy-page)
   - [Service Centres](#510-service-centres-page)
6. [State Management](#6-state-management)
7. [Data Layer](#7-data-layer)
8. [Styling System](#8-styling-system)
9. [Key React Concepts Used](#9-key-react-concepts-used)
10. [How localStorage Works in This App](#10-how-localstorage-works-in-this-app)
11. [Routing — React Router DOM](#11-routing--react-router-dom)
12. [Frontend vs Backend — Important for Viva](#12-frontend-vs-backend--important-for-viva)
13. [Viva Q&A — Likely Questions & Answers](#13-viva-qa--likely-questions--answers)
14. [How to Run Locally](#14-how-to-run-locally)

---

## 1. Project Overview

**AutoPartsHub** is a web-based Car Spare Parts Search and Service System. It allows users to:

- **Search** for spare parts by selecting their car's **brand → model → year → fuel type** (cascading dropdowns — each depends on the previous)
- **Browse & filter** a parts catalogue by type (OEM/Aftermarket) and category
- **Search parts by name** using a live search bar
- **Add parts to a cart** and **update quantities** (increment/decrement)
- **Simulate payment** via UPI, Credit Card, Debit Card, or Net Banking
- **Receive a digital receipt** with an itemised order and print option
- **Learn DIY repair** through step-by-step guides with difficulty ratings
- **Find nearby service centres** with contact info, ratings, and specialisations

> **This is a frontend-only application.** There is no backend server or database. All data is stored in the browser's `localStorage` and in local JavaScript data files.

---

## 2. Tech Stack

| Technology | Version | Role |
|---|---|---|
| **React.js** | 18.2 | UI framework — component-based architecture |
| **React Router DOM** | v6 | Client-side page routing (SPA navigation) |
| **HTML5** | — | Semantic markup via JSX |
| **CSS3** | — | All styling — custom design system, animations |
| **JavaScript (ES6+)** | — | Logic, state, data handling |
| **localStorage** | Browser API | Persisting cart data between page navigations |
| **Google Fonts** | — | Rajdhani (headings) + DM Sans (body) |

### Why React and not plain HTML?
React allows us to split the UI into **reusable components** (e.g., `PartCard` is written once and rendered 12 times). It also provides **reactive state** — when data changes (e.g., cart updates), only the affected component re-renders automatically, without reloading the page.

---

## 3. Project Structure

```
test-test-main/
│
├── public/
│   └── index.html          ← Single HTML file that hosts the entire app
│
├── src/
│   ├── App.js              ← Root component — sets up all routes
│   ├── index.js            ← Entry point — mounts React into index.html
│   │
│   ├── components/
│   │   ├── Navbar.js       ← Navigation bar (shown on every page)
│   │   └── PartCard.js     ← Reusable product card for each spare part
│   │
│   ├── pages/
│   │   ├── Home.js         ← Landing page with cascading vehicle search
│   │   ├── Parts.js        ← Parts catalogue with search + filter
│   │   ├── Carts.js        ← Shopping cart with quantity controls
│   │   ├── Payment.js      ← Dummy payment page (UPI/Card/NetBanking)
│   │   ├── Receipt.js      ← Order confirmation & printable receipt
│   │   ├── DIY.js          ← Step-by-step repair guides
│   │   └── ServiceCenters.js ← Nearby garage/service centre listings
│   │
│   ├── data/
│   │   └── partsData.js    ← Static array of all 12 spare parts
│   │
│   └── styles/
│       └── main.css        ← All CSS — design tokens, layout, components
│
└── package.json            ← Project config and dependencies
```

---

## 4. Architecture & Data Flow

```
┌─────────────────────────────────────────────────────┐
│                     index.html                       │
│              <div id="root"></div>                   │
└──────────────────────┬──────────────────────────────┘
                       │ ReactDOM.createRoot().render()
                       ▼
┌─────────────────────────────────────────────────────┐
│                     App.js                           │
│   <BrowserRouter>                                    │
│     <Navbar />          ← always visible             │
│     <Routes>                                         │
│       / → <Home />                                   │
│       /parts → <Parts />                             │
│       /cart → <Cart />                               │
│       /payment → <Payment />                         │
│       /receipt → <Receipt />                         │
│       /diy → <DIY />                                 │
│       /service → <ServiceCenters />                  │
│     </Routes>                                        │
│   </BrowserRouter>                                   │
└─────────────────────────────────────────────────────┘
                       │
                       │ Data Flow
                       ▼
┌──────────────────────────────────────────────────────┐
│                  partsData.js                         │
│   Static JS array → imported by Parts.js & Cart.js   │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│                  localStorage                         │
│   "cart"       → array of cart items (with quantity) │
│   "lastOrder"  → saved cart snapshot after payment   │
│   "paymentMethod" → "upi" / "credit" / etc.          │
│   "orderId"    → generated order reference number    │
└──────────────────────────────────────────────────────┘
```

**Flow summary:**
1. User visits `/` → selects Brand → Model → Year → Fuel Type → clicks Search → navigates to `/parts`
2. On `/parts`, user browses the catalogue, filters by type/category, searches by name
3. User clicks "Add to Cart" on a `PartCard` → item stored in `localStorage["cart"]`
4. Navbar cart badge reads `localStorage["cart"]` and displays total item count
5. On `/cart`, user adjusts quantities (updates localStorage), clicks Proceed to Payment
6. On `/payment`, user selects a method and submits → cart saved as `lastOrder`, cart cleared
7. `/receipt` reads `lastOrder` and displays the final receipt

---

## 5. Pages & Components — Detailed Breakdown

### 5.1 `App.js` — Root Router

```jsx
<BrowserRouter>
  <Navbar />         {/* Rendered outside <Routes> so it appears on EVERY page */}
  <Routes>
    <Route path="/"        element={<Home />} />
    <Route path="/parts"   element={<Parts />} />
    <Route path="/cart"    element={<Cart />} />
    <Route path="/payment" element={<Payment />} />
    <Route path="/receipt" element={<Receipt />} />
    <Route path="/diy"     element={<DIY />} />
    <Route path="/service" element={<ServiceCenters />} />
  </Routes>
</BrowserRouter>
```

- `BrowserRouter` wraps everything so React Router can track the URL
- `Navbar` is placed **outside** `<Routes>` so it is permanently visible regardless of which page you're on
- `Routes` renders only the component whose `path` matches the current URL
- This is a **Single Page Application (SPA)** — the browser never fully reloads; React swaps components in and out

---

### 5.2 Navbar Component

**File:** `src/components/Navbar.js`

**What it does:**
- Shows the site logo, navigation links, and a cart icon with a live item-count badge
- Uses `useLocation()` to know which page is active and highlight the correct nav link
- Listens to both `storage` events and a custom `cartUpdated` event to refresh the cart count whenever an item is added or removed — even from a different component

**Key React concepts:**
```jsx
const [cartCount, setCartCount] = useState(0);   // state: holds the badge number
const location = useLocation();                   // hook: gives current URL path

useEffect(() => {
  refreshCart();                                  // run on mount
  window.addEventListener("cartUpdated", refreshCart); // listen for cart changes
  return () => window.removeEventListener("cartUpdated", refreshCart); // cleanup
}, []);
```

- `useState` — local reactive variable; when it changes, the component re-renders
- `useEffect` — runs side effects (event listeners, localStorage reads) after render
- The **cleanup function** (the `return` inside `useEffect`) removes event listeners when the component unmounts, preventing memory leaks
- `useLocation()` — a React Router hook that returns the current URL object

**Cart badge quantity:**
```js
const total = cart.reduce((sum, item) => sum + (item.quantity || 1), 0);
```
This sums the `quantity` field of every item in the cart, so adding 3 brake pads shows `3` in the badge.

---

### 5.3 Home Page — Vehicle Search

**File:** `src/pages/Home.js`

**What it does:**
- Hero section with a full-viewport background image
- **4 cascading dropdowns**: Brand → Model → Year → Fuel Type
- Each dropdown is `disabled` until the previous one has a value selected
- Stats bar showing platform highlights
- "Search Spare Parts" button navigates to `/parts`

**The vehicle data structure** (the most important part):
```js
const vehicleData = {
  Toyota: {
    Corolla: {
      years:     ["2024", "2023", "2022", ...],
      fuelTypes: ["Petrol", "Hybrid"]
    },
    Fortuner: {
      years:     ["2024", "2023", ...],
      fuelTypes: ["Diesel", "Petrol"]
    },
    // ...more models
  },
  Honda: { ... },
  Hyundai: { ... },
  // ...more brands
};
```

**Why cascading dropdowns matter:**
Previously (the bug), ALL models were shown regardless of the selected brand because there was no filtering — it was just one flat list of options. Now, selecting a brand computes `models = Object.keys(vehicleData[brand])`, so only that brand's models appear.

**State and derived values:**
```js
const [brand, setBrand] = useState("");
const [model, setModel] = useState("");
const [year,  setYear]  = useState("");
const [fuel,  setFuel]  = useState("");

// Derived — computed from state, not stored separately
const models    = brand        ? Object.keys(vehicleData[brand])                  : [];
const years     = brand&&model ? vehicleData[brand][model]?.years     || []       : [];
const fuelTypes = brand&&model ? vehicleData[brand][model]?.fuelTypes || []       : [];
```

**Reset chain:** When brand changes, model/year/fuel are reset to `""`. When model changes, year/fuel reset. This prevents stale values (e.g., "Corolla 2024 Diesel" remaining selected after switching to Honda).

---

### 5.4 Parts Page — Catalogue

**File:** `src/pages/Parts.js`

**What it does:**
- Live **search bar** — filters parts by name or category as the user types
- **Type filter** buttons — All / OEM / Aftermarket
- **Category filter** buttons — Engine, Brakes, Electrical, Suspension, Cooling, Transmission, Accessories
- Displays a live count of matching parts
- Renders the filtered list of `<PartCard />` components
- Shows an empty state message if no parts match

**Filtering logic:**
```js
const filtered = parts.filter((p) => {
  const matchSearch = p.name.toLowerCase().includes(search.toLowerCase())
                   || p.category.toLowerCase().includes(search.toLowerCase());
  const matchType = typeFilter === "All" || p.type === typeFilter;
  const matchCat  = catFilter  === "All" || p.category === catFilter;
  return matchSearch && matchType && matchCat;
});
```
All three filters are applied simultaneously — they are AND conditions (a part must satisfy all active filters).

---

### 5.5 PartCard Component

**File:** `src/components/PartCard.js`

**What it does:**
- Displays one spare part: image, OEM/Aftermarket badge, name, category, price
- "Add to Cart" button — adds the item to localStorage
- Temporary button state change to "✓ Added" for 2 seconds to give the user feedback

**Cart logic (quantity-aware):**
```js
const addToCart = () => {
  const cart = JSON.parse(localStorage.getItem("cart")) || [];
  const existing = cart.find((item) => item.id === part.id); // check if already in cart

  if (existing) {
    existing.quantity += 1;           // increment if already present
  } else {
    cart.push({ ...part, quantity: 1 }); // add new with quantity 1
  }

  localStorage.setItem("cart", JSON.stringify(cart));
  window.dispatchEvent(new CustomEvent("cartUpdated")); // notify Navbar to refresh badge
};
```

**Custom Event:** `new CustomEvent("cartUpdated")` is dispatched on the `window` object. The Navbar listens for this event. This is how two sibling components (PartCard and Navbar) communicate without passing props between them — they use the global `window` as a message bus.

---

### 5.6 Cart Page

**File:** `src/pages/Carts.js`

**What it does:**
- Reads the cart from `localStorage` on mount
- Shows each item with its image, name, type/category, and a **quantity control** (`−` / count / `+`)
- Dynamically calculates: Subtotal, Shipping (₹99 flat), GST (18%), and Grand Total
- "Remove" button removes an item completely
- Decrementing quantity to 0 automatically removes the item
- "Proceed to Payment" navigates to `/payment`
- Shows an empty cart state with a link back to `/parts`

**Quantity control logic:**
```js
const changeQty = (index, delta) => {
  const updated = cart
    .map((item, i) => i === index ? { ...item, quantity: item.quantity + delta } : item)
    .filter((item) => item.quantity > 0); // removes item if quantity reaches 0
  persist(updated); // saves to localStorage and updates state
};
```

**Dynamic total calculation:**
```js
const subtotal   = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
const shipping   = cart.length > 0 ? 99 : 0;
const taxes      = Math.round(subtotal * 0.18);
const grandTotal = subtotal + shipping + taxes;
```
These are plain JavaScript calculations — they re-run every render since they read from the `cart` state variable.

---

### 5.7 Payment Page

**File:** `src/pages/Payment.js`

**What it does:**
- 4 payment method buttons: UPI, Credit Card, Debit Card, Net Banking
- Each selected method reveals a **different form** with the appropriate fields
- "Pay Now" button simulates 1.5 seconds of processing, then:
  1. Saves cart to `localStorage["lastOrder"]` (so Receipt can read it after cart is cleared)
  2. Saves `paymentMethod` to localStorage
  3. Clears `localStorage["cart"]`
  4. Dispatches `cartUpdated` so Navbar badge resets to 0
  5. Navigates to `/receipt`

**Conditional rendering:**
```jsx
{method === "upi"    && <div className="payment-form">...</div>}
{method === "credit" && <div className="payment-form">...</div>}
{method === "debit"  && <div className="payment-form">...</div>}
{method === "netbanking" && <div className="payment-form">...</div>}
```
Only the selected method's form is rendered in the DOM. This is called **conditional rendering** — a core React pattern using `&&` (logical AND short-circuit).

> **Note:** This is a **dummy/simulated** payment system. No real money is charged. No payment gateway (Razorpay, Stripe, etc.) is integrated.

---

### 5.8 Receipt Page

**File:** `src/pages/Receipt.js`

**What it does:**
- Reads `lastOrder` from localStorage (the cart snapshot saved before clearing)
- Reads `paymentMethod` from localStorage
- Displays: animated success checkmark, order ID, itemised list with quantities, subtotal, shipping, GST, grand total, payment method
- "Print Receipt" button calls `window.print()` — triggers the browser's native print dialog
- "Continue Shopping" link returns to `/parts`

**Why `lastOrder` instead of `cart`?**
Because Payment.js clears `localStorage["cart"]` before navigating here. If Receipt tried to read `cart`, it would be empty. Saving a snapshot to `lastOrder` first preserves the order data.

---

### 5.9 DIY Page

**File:** `src/pages/DIY.js`

**What it does:**
- Displays 8 repair guides stored as a JavaScript array of objects inside the component
- Each guide has: part name, difficulty (easy/medium/hard), estimated time, and numbered steps
- Difficulty badges are colour-coded: green (easy), gold (medium), red (hard)
- No external API — all data is local (as specified in the project report)

**Data structure:**
```js
const guides = [
  {
    part: "Brake Pads",
    difficulty: "medium",
    time: "45–60 min",
    steps: [
      "Loosen the wheel lug nuts slightly before lifting the car.",
      // ...
    ]
  },
  // ...7 more guides
];
```

---

### 5.10 Service Centres Page

**File:** `src/pages/ServiceCenters.js`

**What it does:**
- Displays 6 service centres stored as a local JavaScript array
- Each card shows: name, type, star rating, specialisation tags, location, working hours, phone number
- "Call Now" button uses `window.location.href = "tel:..."` to initiate a phone call on mobile
- No map integration (static data, as per project scope)

---

## 6. State Management

This app uses **local component state only** (no Redux, no Context API). Each page manages its own state using React's `useState` hook.

| Component | State Variables | What they track |
|---|---|---|
| `Navbar` | `cartCount`, `menuOpen` | Badge number, mobile menu open/close |
| `Home` | `brand`, `model`, `year`, `fuel` | Search form selections |
| `Parts` | `search`, `typeFilter`, `catFilter` | Active filters |
| `PartCard` | `added` | Temporary "Added!" button state |
| `Cart` | `cart` | Array of items from localStorage |
| `Payment` | `method`, `processing` | Selected payment method, loading state |

**Cross-component communication** is handled via:
1. `localStorage` — Cart data persists and is readable by any page
2. `CustomEvent("cartUpdated")` on `window` — Lets Navbar know to refresh its badge when PartCard adds an item

---

## 7. Data Layer

### `src/data/partsData.js`
A plain JavaScript array exported as the default export. Each object represents one spare part:

```js
{
  id: 1,
  name: "Brake Pads",
  type: "OEM",          // "OEM" or "Aftermarket"
  category: "Brakes",   // used for category filter
  price: 3500,          // in Indian Rupees
  image: "https://cdn-icons-png.flaticon.com/..."
}
```

This file is **imported** (not fetched from a server) by `Parts.js` and available to `PartCard.js` via props.

### Vehicle Data (inside `Home.js`)
A nested JavaScript object — not a separate file — mapping:
`Brand → Model → { years: [...], fuelTypes: [...] }`

This structure allows the dropdowns to be completely data-driven: add a new brand by adding one key to `vehicleData`.

---

## 8. Styling System

**File:** `src/styles/main.css`

All styling is written in plain CSS with **CSS custom properties (variables)** defined on `:root`:

```css
:root {
  --bg:          #08080A;   /* near-black background */
  --bg-card:     #121218;   /* card/surface colour */
  --gold:        #D4A843;   /* primary accent — used for highlights, prices, CTAs */
  --red:         #E63946;   /* danger/remove actions */
  --green:       #22C55E;   /* success/OEM badge */
  --text:        #F2F0EB;   /* primary text */
  --text-2:      #9A9AA5;   /* secondary/muted text */
  --border:      rgba(255,255,255,0.06);
  --radius:      10px;      /* consistent border radius */
  --transition:  0.2s ease; /* consistent animation speed */
}
```

**Why CSS variables?**
Changing `--gold` in one place updates every button, price, and accent across the entire site. It's the CSS equivalent of a constant.

**Typography:**
- `Rajdhani` — angular, technical sans-serif for all headings (`h1`–`h5`), logo, prices
- `DM Sans` — clean geometric sans for all body text, buttons, labels
- Imported from Google Fonts in `public/index.html`

**Animations:**
All animations are pure CSS — no JavaScript animation libraries:
```css
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(28px); }
  to   { opacity: 1; transform: translateY(0); }
}
/* Applied to .hero-overlay on page load */

@keyframes popIn {
  from { transform: scale(0.5); opacity: 0; }
  to   { transform: scale(1); opacity: 1; }
}
/* Applied to the success checkmark on the receipt page */
```

**Responsive Design:**
```css
@media (max-width: 768px) {
  .search-selects { grid-template-columns: 1fr 1fr; } /* 2 cols on tablet */
  .nav-links { display: none; }  /* hide nav links */
  .hamburger { display: flex; }  /* show hamburger menu */
}
```

---

## 9. Key React Concepts Used

### Hooks
| Hook | Where Used | Purpose |
|---|---|---|
| `useState` | All components | Store and update local component data |
| `useEffect` | Navbar, Cart | Run code after render (read localStorage, set up event listeners) |
| `useNavigate` | Home, Cart, Payment | Programmatically navigate to a different route |
| `useLocation` | Navbar | Read current URL to highlight the active nav link |

### JSX
JSX is the HTML-like syntax used inside React components. It is **not HTML** — it is compiled by Babel into `React.createElement()` calls.

```jsx
// JSX (what you write)
<div className="card">Hello</div>

// What Babel compiles it to
React.createElement("div", { className: "card" }, "Hello")
```

Note: `class` becomes `className` in JSX (because `class` is a reserved word in JavaScript).

### Props
Props are how parent components pass data to child components:
```jsx
// Parent (Parts.js)
<PartCard part={p} />

// Child (PartCard.js)
function PartCard({ part }) {
  return <h3>{part.name}</h3>; // "part" received as prop
}
```

### Conditional Rendering
```jsx
{cart.length === 0 ? <EmptyState /> : <CartItems />}  // ternary
{method === "upi" && <UpiForm />}                      // short-circuit AND
```

### List Rendering
```jsx
{parts.map((p) => (
  <PartCard key={p.id} part={p} />
))}
```
The `key` prop is **required** when rendering lists — React uses it to identify which items changed, so it can update the DOM efficiently without re-rendering the entire list.

---

## 10. How localStorage Works in This App

`localStorage` is a browser API that stores key-value pairs as strings. Data persists even after closing the tab (unlike `sessionStorage`).

| Key | Stored When | Read By | Cleared When |
|---|---|---|---|
| `"cart"` | PartCard: Add to Cart | Navbar, Cart, Payment | Payment success |
| `"lastOrder"` | Payment: before clearing cart | Receipt | Never (stays for reference) |
| `"paymentMethod"` | Payment: on submit | Receipt | Never |
| `"orderId"` | Receipt: on first load | Receipt | Never |

**Writing:**
```js
localStorage.setItem("cart", JSON.stringify(cart)); // must stringify — localStorage only stores strings
```

**Reading:**
```js
const cart = JSON.parse(localStorage.getItem("cart")) || []; // parse back to array; default to [] if null
```

---

## 11. Routing — React Router DOM

This app uses **React Router DOM v6** for client-side routing.

**What is client-side routing?**
In a traditional website, clicking a link sends a request to a server, which returns a new HTML page. In a React SPA, clicking a `<Link>` updates the URL in the browser **without** sending a server request. React Router intercepts the URL change and renders the matching component.

**Core components used:**

| Component | Purpose |
|---|---|
| `<BrowserRouter>` | Wraps the entire app; provides routing context |
| `<Routes>` | Container for all route definitions |
| `<Route path="/" element={<Home/>}>` | Maps a URL path to a component |
| `<Link to="/parts">` | Renders an `<a>` tag that uses client-side navigation |

**Hooks used:**

| Hook | Returns | Used In |
|---|---|---|
| `useNavigate()` | `navigate` function | Home, Cart, Payment — programmatic navigation |
| `useLocation()` | current location object | Navbar — to detect active route |

---

## 12. Frontend vs Backend — Important for Viva

### This Project is Frontend-Only

| Aspect | This Project | A Full-Stack Project Would Have |
|---|---|---|
| **Language** | JavaScript (React) only | JavaScript/Python/Java on server |
| **Data storage** | Browser `localStorage` | Database (MongoDB, MySQL, PostgreSQL) |
| **Parts data** | Static JS file (`partsData.js`) | REST API → `GET /api/parts` |
| **Cart** | `localStorage` | Server-side session or database cart |
| **Payment** | Simulated (no real gateway) | Razorpay / Stripe API integration |
| **Authentication** | None | JWT tokens / OAuth |
| **Server** | None (just `npm start` dev server) | Node.js/Express, Django, Spring Boot |

### Why No Backend?
The project report specifies: *"HTML, CSS, JavaScript, and React.js"* as the software requirements. A backend with database integration is mentioned as a **future enhancement** — *"allowing future enhancements such as database integration using technologies like MongoDB"*.

### What Would a Backend Add?
1. **MongoDB** could store the parts catalogue — fetched via `fetch('/api/parts')` instead of importing a static file
2. **User accounts** — login, order history
3. **Real payment gateway** — Razorpay/Stripe API calls
4. **Admin panel** — add/edit/remove parts

---

## 13. Viva Q&A — Likely Questions & Answers

### React Fundamentals

**Q: What is React?**
A: React is an open-source JavaScript library developed by Meta (Facebook) for building user interfaces. It uses a component-based architecture where the UI is broken into small, reusable pieces called components. React uses a Virtual DOM to efficiently update only the parts of the page that changed, instead of reloading the entire page.

**Q: What is a component?**
A: A component is a self-contained piece of UI with its own logic and styling. In this project, `PartCard` is a component — it is written once and reused 12 times (once for each spare part). Components are JavaScript functions that return JSX.

**Q: What is JSX?**
A: JSX (JavaScript XML) is a syntax extension that lets you write HTML-like code inside JavaScript. It is not valid JavaScript on its own — it is transformed by Babel into `React.createElement()` calls before running in the browser. For example, `<h1>Hello</h1>` in JSX becomes `React.createElement('h1', null, 'Hello')`.

**Q: What is state? How is it different from a variable?**
A: State is a reactive variable managed by React. When state changes, React automatically re-renders the component. A plain JavaScript variable does not trigger a re-render. `useState` is the hook used to create state:
```js
const [count, setCount] = useState(0); // count is state, setCount updates it
```

**Q: What is `useEffect`?**
A: `useEffect` is a hook for running side effects — code that interacts with something outside the component (localStorage, event listeners, APIs). In this app, `useEffect` in the Navbar reads `localStorage` to get the cart count, and sets up an event listener for cart updates. The dependency array `[]` means it runs only once (on mount).

**Q: What is the Virtual DOM?**
A: The Virtual DOM is an in-memory representation of the real DOM. When state changes, React creates a new Virtual DOM, compares it with the previous one (diffing algorithm), and updates only the changed parts in the real DOM. This is much faster than rewriting the entire DOM on every state change.

**Q: What are props?**
A: Props (properties) are how data is passed from a parent component to a child. They are read-only — a child cannot modify its own props. In this app, `Parts.js` passes each part object to `<PartCard part={p} />`, and PartCard receives it as `{ part }`.

---

### Routing

**Q: What is React Router? Why use it?**
A: React Router is a library that enables client-side routing in React SPAs. Instead of the browser requesting a new page from a server on every navigation, React Router intercepts URL changes and renders the correct component. This makes navigation instant with no page reload.

**Q: What is the difference between `<Link>` and `<a>`?**
A: An HTML `<a>` tag causes a full page reload (sends a new HTTP request to the server). A React Router `<Link>` updates the URL and renders the matching component without any server request or page reload.

**Q: What is `useNavigate`?**
A: `useNavigate` returns a `navigate` function for programmatic navigation — navigating in code, not via a clicked link. For example, after the user clicks "Search Parts" on the Home page, `navigate("/parts")` is called to redirect them to the parts catalogue.

---

### Data & Storage

**Q: Where is the data stored in this application?**
A: Part data (catalogue) is stored in a static JavaScript file (`partsData.js`). Cart data is stored in the browser's `localStorage` — a key-value storage in the browser that persists across page navigations and tab closes.

**Q: What is localStorage?**
A: `localStorage` is a browser API that stores data as strings. It persists until explicitly cleared — unlike `sessionStorage` which clears when the tab is closed. In this app, cart items are stored as a JSON string: `localStorage.setItem("cart", JSON.stringify(cartArray))` and read back with `JSON.parse(localStorage.getItem("cart"))`.

**Q: Why do you use `JSON.stringify` and `JSON.parse` with localStorage?**
A: Because `localStorage` can only store strings. An array or object must be converted to a JSON string with `JSON.stringify` to store, and parsed back with `JSON.parse` to retrieve as a usable JavaScript object.

**Q: How does the Navbar know when a new item is added to the cart?**
A: When `PartCard` adds an item, it dispatches a custom event: `window.dispatchEvent(new CustomEvent("cartUpdated"))`. The Navbar has an event listener: `window.addEventListener("cartUpdated", refreshCart)`. When the event fires, the Navbar reads localStorage and updates its badge count. This is a publish-subscribe pattern using the browser's `window` as the event bus.

---

### CSS & Styling

**Q: What are CSS custom properties (variables)?**
A: CSS variables (defined with `--`) store reusable values. In this app, `--gold: #D4A843` is the gold accent color. Using it as `color: var(--gold)` everywhere means changing the color in one place updates the whole site. They are defined on `:root` to make them globally available.

**Q: What is CSS Grid? Where is it used?**
A: CSS Grid is a 2D layout system. In this app it is used for:
- The vehicle search selects: `grid-template-columns: repeat(4, 1fr)` — 4 equal-width columns
- The parts catalogue: `grid-template-columns: repeat(auto-fill, minmax(260px, 1fr))` — as many columns as fit, each at least 260px wide
- The cart layout: `grid-template-columns: 1fr 360px` — main content + fixed sidebar

**Q: What is `backdrop-filter` in the Navbar?**
A: `backdrop-filter: blur(16px)` applies a blur effect to the content **behind** the element, creating a frosted-glass effect. This makes the sticky navbar legible over the hero image.

---

### General

**Q: What is a SPA (Single Page Application)?**
A: An SPA loads one HTML file once (`index.html`). After that, all navigation happens in JavaScript — new "pages" are actually different React components rendered in the same HTML shell. The URL changes (React Router handles this), but no new HTML is fetched from a server. Benefits: faster navigation, smoother UX.

**Q: What is `npm start` doing?**
A: `npm start` runs `react-scripts start`, which starts a local development server (Webpack Dev Server) on `localhost:3000`. It compiles JSX and modern JavaScript into browser-compatible JavaScript, watches for file changes, and hot-reloads the browser automatically.

**Q: What is `package.json`?**
A: `package.json` is the project manifest. It lists the project name, version, all dependencies (libraries the project needs), and scripts like `start`, `build`, and `test`. Running `npm install` reads this file and downloads all listed packages into `node_modules/`.

**Q: What is the purpose of `public/index.html`?**
A: It is the single HTML file that serves the entire application. It contains `<div id="root"></div>` — the mount point where React injects the entire app via `ReactDOM.createRoot(document.getElementById('root')).render(<App />)`. The HTML file itself has almost no content; React fills it dynamically.

**Q: Explain the cart flow from adding an item to seeing the receipt.**
A:
1. User clicks "Add to Cart" on a `PartCard`
2. PartCard reads cart from localStorage, adds/increments the item, saves back, dispatches `cartUpdated` event
3. Navbar's event listener fires → reads localStorage → updates badge count
4. User visits `/cart` — Cart reads localStorage on mount via `useEffect`
5. User adjusts quantities — each change updates localStorage
6. User clicks "Proceed to Payment" → navigated to `/payment`
7. User picks a method and clicks "Pay Now" — `lastOrder` is set in localStorage, `cart` is cleared, navigate to `/receipt`
8. Receipt reads `lastOrder` and `paymentMethod` from localStorage and displays the final summary

---

## 14. How to Run Locally

### Prerequisites
- Node.js (v16 or above)
- npm

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/Nike112/autopartshub.git
cd autopartshub

# 2. Install dependencies
npm install

# 3. Start the development server
npm start

# 4. Open in browser
# http://localhost:3000
```

### Available Scripts

| Command | Description |
|---|---|
| `npm start` | Runs app in development mode at `localhost:3000` |
| `npm run build` | Builds the app for production into the `build/` folder |
| `npm test` | Launches the test runner |

---

## References

1. MDN Web Docs — HTML, CSS, and JavaScript Documentation. Mozilla Foundation. https://developer.mozilla.org
2. W3Schools — Web Development Tutorials. https://www.w3schools.com
3. React Official Documentation — Getting Started with React. https://react.dev
4. Flanagan, D. — *JavaScript: The Definitive Guide*. 7th Edition, O'Reilly Media, 2020.
5. Laudon, K.C. and Traver, C.G. — *E-Commerce: Business, Technology, Society*. 16th Edition, Pearson Education, 2021.

---

*Built with React.js · Submitted for academic evaluation · VIT Chennai*
