# User Story: Customer Manages Cart

**Story ID**: SC-004 (BRD-2026-06-10-smart-checkout-STORY-004)  
**Feature**: Shopping Cart Management  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** customer using Smart Checkout  
**I want to** view my cart, see item details, and remove items I don't want  
**So that** I can verify my purchases before paying and adjust quantities

---

## 2. Story Scope

### In Scope
- Display all scanned items in cart (name, qty, unit price, line total)
- Remove item from cart (set qty = 0)
- View running cart total (sum of all line items)
- View cart count badge (number of items)
- Edit cart is read-only for quantity (customer cannot manually edit)
- Clear entire cart (optional - one-tap clear all)

### Out of Scope
- Manual item entry by customer
- Quantity adjustment UI (customer can only remove, not increase qty via cart)
- Apply coupon/discount codes
- Save cart for later

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: View Cart After Scanning
  Given the customer has scanned 3 items
  When the customer opens the cart view
  Then the app displays all 3 items with:
  - Item name
  - Quantity (1 per item, or higher if scanned multiple times)
  - Unit price
  - Line total (qty × unit price)
  And the cart total is displayed at the bottom
  And a "Proceed to Checkout" button is visible

Scenario 2: Remove Item from Cart
  Given the cart is open with 3 items
  When the customer taps the "Remove" button on an item
  Then the item is removed from the cart
  And the cart now shows 2 items
  And the cart total is recalculated immediately
  And the running cart total is updated on the main scanning screen

Scenario 3: Cart Badge Updates
  Given the customer is on the main scanning screen
  When a cart badge or counter is visible (e.g., "Items: 3")
  And the customer scans a new item
  Then the badge updates to "Items: 4" immediately
  And if the customer removes an item from cart
  Then the badge updates to "Items: 3"

Scenario 4: Cart Persistence
  Given the customer has scanned items and sees them in cart
  When the customer navigates away from the cart screen and returns
  Then the cart items are still visible (state persisted)
  And the totals have not changed

Scenario 5: Clear All Items
  Given the cart has 5 items
  When the customer taps "Clear Cart" (if available)
  Then a confirmation modal appears: "Remove all items from cart? This cannot be undone."
  And if confirmed, all items are removed
  And the cart total becomes $0
  And the cart badge shows "Items: 0"
```

---

## 4. Technical Requirements

- **State Management**: Redux/MobX to persist cart state
- **Cart Data Structure**: Array of {itemId, name, qty, unitPrice, lineTotal}
- **Real-Time Updates**: Scan → immediately update cart UI
- **Calculation Engine**: Accurate line total (qty × unitPrice) and cart total
- **Local Storage**: Persist cart to SQLite in case of app crash/restart
- **Badge Updates**: Real-time cart count display

---

## 5. Dependencies

### Blocks
- SC-005 (Cart Totals & Tax Display)
- SC-006 (Proceed to Checkout)

### Blocked By
- SC-002 (Barcode Scanning)
- SC-003 (QR Code Scanning)

---

## 6. Story Points & Breakdown

**Story Points**: 5

| Component | Points | Rationale |
|-----------|--------|-----------|
| **Cart UI Design & Layout** | 1.5 | List view, item cards, remove buttons |
| **State Management** | 1.5 | Redux/MobX setup, cart actions (add, remove, update) |
| **Persistence & Recovery** | 1 | Local storage, crash recovery |
| **Testing & Error Handling** | 1.5 | Unit tests for cart logic, UI tests |

**Total**: 5 points

---

## 7. MoSCoW Classification

**MOSCOW**: MUST  
**Rationale**: Essential feature. Customer must be able to review and modify cart before payment. Core to user experience.

---

## 8. Definition of Done

- [ ] Cart UI displays all items with correct details
- [ ] Remove item functionality working correctly
- [ ] Cart totals calculated accurately
- [ ] Cart badge updates in real-time
- [ ] Cart state persists across app sessions
- [ ] Unit tests for cart state management (>85% coverage)
- [ ] UI tests for cart display, remove, clear operations
- [ ] QA sign-off received
- [ ] No blocking bugs

---

## Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-1.5 | Cart Display (real-time) |
| FR-1.6 | Cart Editing (remove items) |

