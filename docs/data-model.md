# Data Model

The model is organized around a marketplace: **Vendors** own **Products**, shoppers
(**Users**) fill a **Cart**, and checkout produces an **Order** whose **OrderItems**
may span multiple vendors, each settled through a **Payment** and per-vendor **Payout**.

## Entities

For each entity: its purpose and the fields that matter (not every column).

### User
- **Purpose:** an account — customer, vendor owner, or admin.
- **Key fields:** `id`, `email: unique`, `passwordHash` (if credentials), `role: enum(CUSTOMER|VENDOR|ADMIN)`, `name`, `createdAt`.

### Vendor
- **Purpose:** a seller's storefront within the marketplace.
- **Key fields:** `id`, `ownerId → User`, `name`, `slug: unique`, `description`, `stripeAccountId` (Connect), `status: enum(PENDING|ACTIVE|SUSPENDED)`.

### Category
- **Purpose:** taxonomy for browsing/filtering.
- **Key fields:** `id`, `name`, `slug: unique`, `parentId → Category?` (nestable).

### Product
- **Purpose:** an item a vendor sells.
- **Key fields:** `id`, `vendorId → Vendor`, `categoryId → Category`, `title`, `slug`, `description`, `priceCents: int`, `currency`, `inventory: int`, `images: string[]`, `status: enum(DRAFT|ACTIVE|ARCHIVED)`.

### Cart / CartItem
- **Purpose:** in-progress selection before checkout.
- **Key fields (Cart):** `id`, `userId → User?` (or anonymous session id).
- **Key fields (CartItem):** `id`, `cartId → Cart`, `productId → Product`, `quantity`, `unitPriceCents` (snapshot at add time; re-verified at checkout).

### Order / OrderItem
- **Purpose:** a confirmed purchase. One Order can include items from many vendors.
- **Key fields (Order):** `id`, `userId → User`, `status: enum(PENDING|PAID|FULFILLED|CANCELLED|REFUNDED)`, `totalCents`, `currency`, `createdAt`.
- **Key fields (OrderItem):** `id`, `orderId → Order`, `vendorId → Vendor`, `productId → Product`, `quantity`, `unitPriceCents`, `subtotalCents`, `fulfillmentStatus`.

### Payment / Payout
- **Purpose:** the money trail. Payment = the charge; Payout = each vendor's share.
- **Key fields (Payment):** `id`, `orderId → Order`, `stripePaymentIntentId: unique`, `amountCents`, `status`.
- **Key fields (Payout):** `id`, `orderId → Order`, `vendorId → Vendor`, `amountCents`, `platformFeeCents`, `stripeTransferId`, `status`.

### Address
- **Purpose:** shipping/billing target for an order.
- **Key fields:** `id`, `userId → User`, `line1`, `line2`, `city`, `region`, `postalCode`, `country`.

## Relationships

- User **has many** Vendors (typically one) and **has many** Orders.
- Vendor **has many** Products; Vendor **has many** Payouts.
- Category **has many** Products; Category **has many** child Categories.
- Cart **has many** CartItems; CartItem **belongs to** a Product.
- Order **has many** OrderItems; OrderItem **belongs to** a Vendor and a Product.
- Order **has one** Payment and **has many** Payouts (one per participating vendor).

## Indexes & constraints

- `User.email`, `Vendor.slug`, `Product.slug`, `Payment.stripePaymentIntentId` are unique.
- Index `Product.vendorId`, `Product.categoryId`, `OrderItem.vendorId`, `OrderItem.orderId` for the common queries.
- All money fields are integer cents; never floats.

## Lifecycle notes

- Timestamps (`createdAt`/`updatedAt`) on all mutable tables.
- Prefer soft-delete (`status` enums above) over hard deletes for Products/Vendors/Orders so history stays intact.
- Migrations live in `prisma/migrations/`; the schema source of truth is `prisma/schema.prisma`.
