# CAP LUXE — E-Commerce Website

A modern, luxury e-commerce storefront for CAP LUXE — premium caps for the confident.
Built with React, React Router, and Paystack for Ghana-based payments.

---

## 1. Setup

**Requirements:** Node.js 18+ and npm installed on your machine.

```bash
# 1. Install dependencies
npm install

# 2. Add your Paystack public key
cp .env.example .env
# then open .env and paste your real key:
# VITE_PAYSTACK_PUBLIC_KEY=pk_test_xxxxxxxxxxxxxxxxxxxx

# 3. Run the dev server
npm run dev
```

The site opens at `http://localhost:5173`.

To build for production:
```bash
npm run build      # outputs to /dist
npm run preview    # preview the production build locally
```

Deploy the `/dist` folder to any static host: Vercel, Netlify, Cloudflare Pages, or your own server.

---

## 2. Paystack setup (required for checkout to work)

1. Create a free account at [paystack.com](https://paystack.com).
2. Go to **Settings → API Keys & Webhooks**.
3. Copy your **Public Key** (starts with `pk_test_` while testing, `pk_live_` when you go live).
4. Paste it into `.env` as shown above.
5. Restart `npm run dev` after editing `.env` — Vite only reads env files on startup.

Without a real key, the checkout page will show a clear message asking you to configure payments, instead of failing silently.

### Important: server-side verification (read before going live)

This build uses **Paystack Inline (Popup)**, which works entirely from the browser — no backend required to collect payment. This is the fastest way to get a real store live.

However, the success callback in `src/lib/paystack.js` currently trusts the browser's report of a successful payment. For a production store handling real money, you should eventually add a small backend (even a single serverless function) that:

1. Receives the payment `reference` after checkout.
2. Calls `GET https://api.paystack.co/transaction/verify/:reference` using your **secret key** (never expose this in frontend code).
3. Only then marks the order as paid and triggers fulfillment (email, stock update, etc.).

This stops a tampered client from faking a successful order. The code is structured so this slots in cleanly later — look for the comment block in `src/lib/paystack.js`.

---

## 3. File structure

```
cap-luxe/
├── index.html                  # Entry HTML, loads fonts + Paystack script
├── vite.config.js
├── package.json
├── .env.example                 # Copy to .env and add your Paystack key
│
└── src/
    ├── main.jsx                 # App entry point
    ├── App.jsx                  # Routes + layout shell (header/footer/cart/whatsapp)
    │
    ├── styles/
    │   └── tokens.css           # Design tokens: colors, type, buttons, hex motif
    │
    ├── data/
    │   └── products.js          # Product catalog — EDIT THIS to add/change caps
    │
    ├── context/
    │   └── CartContext.jsx      # Global cart state (add/remove/update, persisted)
    │
    ├── lib/
    │   └── paystack.js          # Paystack Inline integration
    │
    ├── hooks/
    │   └── useReveal.js         # Scroll-reveal animation hook
    │
    ├── components/
    │   ├── Header.jsx           # Nav bar, mobile menu, cart icon
    │   ├── Footer.jsx           # Footer with shop/social links
    │   ├── CartDrawer.jsx       # Slide-out cart panel
    │   ├── ProductCard.jsx      # Reusable product grid card
    │   ├── ProductImage.jsx     # CSS-rendered cap visual (see note below)
    │   ├── WhatsAppButton.jsx   # Floating WhatsApp chat button
    │   └── ScrollToTop.jsx      # Resets scroll position on route change
    │
    └── pages/
        ├── Home.jsx              # Hero, featured caps, brand story preview
        ├── Shop.jsx               # Product grid with category filtering
        ├── ProductDetail.jsx     # Size/color selection, add to cart, buy now
        ├── About.jsx              # Full brand story
        ├── Contact.jsx            # Contact form + social + WhatsApp
        ├── Checkout.jsx           # Shipping form, order summary, Paystack pay
        ├── CheckoutSuccess.jsx   # Order confirmation screen
        └── NotFound.jsx           # 404 page
```

---

## 4. Things you'll likely want to customize

**Product photos.** No product photography was supplied, so `ProductImage.jsx` renders a stylized cap illustration in pure CSS/SVG that matches your logo's line-art style. To use real photos:
1. Add images to `src/assets/products/`.
2. In `src/data/products.js`, add an `image: '/src/assets/products/your-photo.jpg'` field to each product.
3. Swap `<ProductImage tone={product.tone} />` for an `<img src={product.image} />` tag in `ProductCard.jsx` and `ProductDetail.jsx`.

**WhatsApp number.** Currently set to a placeholder (`233000000000`) in two places:
- `src/components/WhatsAppButton.jsx`
- `src/pages/Contact.jsx`

Replace with your real number, country code first, digits only.

**Instagram / TikTok links.** Placeholder URLs in `src/components/Footer.jsx` and `src/pages/Contact.jsx` — update with your real profile links.

**Shipping rate.** Flat rate is set in `src/pages/Checkout.jsx` (`SHIPPING_FLAT_RATE = 25`). Adjust or make it dynamic by region as you grow.

**Products.** Everything in the catalog lives in `src/data/products.js` — add, remove, or edit caps, prices (in GHS), colors, and sizes there. The whole site (Home, Shop, filters, Product pages) reads from this one file.

**Contact form.** The form in `src/pages/Contact.jsx` currently simulates submission (no backend). To make it send real emails, connect it to a service like Formspree, Resend, or your own endpoint — the `handleSubmit` function is where that POST request goes.

---

## 5. What's already built

- Fully responsive, mobile-first layout across all pages
- Cart system with persistent storage (survives page refresh, via localStorage)
- Category filtering on the Shop page (Snapbacks / Fitted / Dad Caps / All)
- Product detail pages with color swatches, size selection, quantity, Add to Bag, and Buy Now
- Full checkout flow: shipping form → order summary → Paystack payment → confirmation page
- WhatsApp floating chat button for quick orders
- Scroll-triggered reveal animations, hover micro-interactions, smooth page transitions
- SEO meta tags (title, description, Open Graph) in `index.html`
- Accessible focus states, skip-to-content link, semantic HTML throughout

Enjoy the launch. 🖤
