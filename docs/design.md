# Design

Clean, content-first, trustworthy commerce — products are the hero, chrome stays
quiet. Three distinct surfaces (storefront, vendor dashboard, admin) share one
design system but signal their context clearly.

## Key screens

- **Home / catalog** — featured + browsable product grid with category and price filters; search bar in the header.
- **Search results** — same grid, driven by a query; shows result count and active filters.
- **Product detail** — image gallery, title, price, vendor link, quantity, add-to-cart; vendor/store info and shipping note.
- **Cart** — line items grouped by vendor, quantities editable, running total, checkout button.
- **Checkout** — address + Stripe payment element; order summary; confirmation screen on success.
- **Account / orders** — order history with status; profile and addresses.
- **Vendor dashboard** — product CRUD table, orders for this vendor, Stripe payout/onboarding status.
- **Admin panel** — vendor approvals, product moderation, all-orders view, platform-fee setting.

## Layout & navigation

- **Storefront:** top header (logo, search, cart, account) + main content grid; minimal footer.
- **Vendor & admin:** persistent left sidebar (sections) + top bar with role/context indicator, so it's obvious you're not on the public store.

## Components

- **ProductCard** — image, title, price, vendor — used across catalog/search/home.
- **CartLineItem** — product row with quantity stepper and remove, grouped under a vendor header.
- **PriceTag** — formats integer cents to currency consistently everywhere.
- **DataTable** — sortable/filterable table for vendor products and admin lists.
- **StatusBadge** — order/vendor/product status pills with consistent colors.

## Visual style

- Color: neutral base (white/zinc) with a single brand accent for primary actions; reserve red/green/amber for status only.
- Typography: one sans-serif family; clear scale (display / heading / body / caption).
- Spacing & density: comfortable, card-based grid on the storefront; denser tables in dashboards.
- States: every list/page defines empty, loading (skeletons), and error states.

## Responsiveness & accessibility

- Mobile-first; product grid reflows 1→2→3+ columns by breakpoint.
- Keyboard-navigable throughout; visible focus states; WCAG AA contrast.
- All product/vendor images carry meaningful `alt` text; forms have associated labels.
