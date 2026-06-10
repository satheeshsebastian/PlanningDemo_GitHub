# User Story: Store Manager Monitors Transactions & Fraud

**Story ID**: SC-009 (BRD-2026-06-10-smart-checkout-STORY-009)  
**Feature**: Store Manager Dashboard  
**Sprint**: MVP (Pilot)

---

## 1. User Story Statement

**As a** store manager in a pilot store  
**I want to** view real-time transaction activity, fraud alerts, and failed exit validations on a dashboard  
**So that** I can monitor system health, prevent fraud, and handle escalations quickly

---

## 2. Story Scope

### In Scope
- Real-time transaction list (in-progress, completed, failed)
- Fraud alerts (amount limit exceeded, velocity check triggered)
- Failed exit validation alerts
- Transaction detail view (items, total, payment method)
- Override/cancel transaction capability
- Escalation queue status
- Transaction history and logs (filterable)
- Manager authentication

### Out of Scope
- Analytics/reporting (defer to v1.1)
- Historical data export
- Custom dashboards/personalization
- User role management

---

## 3. Acceptance Criteria

```gherkin
Scenario 1: Manager Logs Into Dashboard
  Given the store manager has installed the dashboard app/web interface
  When the manager logs in with credentials
  Then the manager is authenticated
  And the real-time transaction dashboard displays
  And all active transactions are visible

Scenario 2: View Active Transactions
  Given the dashboard is open
  When the manager views the "Active Transactions" section
  Then a list shows: Transaction ID, Item count, Total, Customer device, Status
  And sorting options are available (by time, by total, by status)
  And detailed view button is available for each transaction

Scenario 3: Fraud Alert - Amount Limit
  Given a customer attempts a transaction > $200
  When the amount limit is triggered
  Then the dashboard displays a red alert: "FRAUD ALERT: Amount exceeds $200 limit"
  And the alert shows: Transaction ID, Amount, Timestamp
  And the transaction is blocked (customer app shows error)
  And the manager can view the transaction for review

Scenario 4: Fraud Alert - Velocity Check
  Given a device initiates >5 transactions in 10 minutes
  When the 6th transaction is attempted
  Then the dashboard displays: "VELOCITY ALERT: Too many transactions from device [ID]"
  And the transaction is blocked
  And the manager can view transaction history from the device
  And the manager can override (whitelist) the device if needed

Scenario 5: Override Transaction
  Given a transaction is displayed on the dashboard
  When the manager selects "Override" or "Cancel"
  Then a confirmation modal appears: "Cancel this transaction? Refund will be processed."
  And if confirmed, the transaction is cancelled
  And a refund is initiated via Stripe
  And the override is logged with timestamp and reason

Scenario 6: Exit Validation Alert
  Given a customer's exit validation failed (item mismatch)
  When the alert is triggered
  Then the dashboard displays: "EXIT ALERT: Verification failed for [Transaction ID]"
  And details show: Expected items vs. what was at exit
  And the manager can view the exit validation log
  And the manager can override to allow customer to exit
  And the override is logged

Scenario 7: Escalation Queue
  Given store associates have escalated failed scans
  When the manager views the "Escalations" section
  Then a queue shows: Customer location, Time pending, Associate assigned, Status
  And the manager can reassign or resolve escalations
  And resolved escalations are removed from queue

Scenario 8: Transaction History & Filtering
  Given the manager wants to review past transactions
  When the manager views "Transaction History"
  Then a searchable/filterable list shows: all transactions from the day
  And filters available: Date range, Amount range, Status (completed/cancelled), Payment method
  And detailed view shows: items, prices, receipt, exit validation status
```

---

## 4. Technical Requirements

- **Dashboard Frontend**: React web app or React Native mobile app
- **Real-Time Updates**: WebSocket connection for live transaction feed
- **Authentication**: Store manager login (OAuth or API key)
- **Backend API**: REST endpoints for transactions, alerts, override operations
- **Fraud Logic**: Server-side amount limits, velocity checks
- **Data Persistence**: Log all manager actions (overrides, cancellations)
- **Responsive Design**: Works on tablet (primary) and desktop

---

## 5. Dependencies

### Blocks
- None (independent monitoring tool)

### Blocked By
- SC-005 (Payment Processing - transactions must exist to monitor)
- SC-007 (Exit Validation - exit alerts require this)

---

## 6. Story Points & Breakdown

**Story Points**: 13

| Component | Points | Rationale |
|-----------|--------|-----------|
| **Dashboard UI & Layout** | 3 | Multiple sections (active txns, alerts, history, escalations) |
| **Real-Time WebSocket Integration** | 2 | Live transaction feed, alert notifications |
| **Fraud Alert Logic & Display** | 2 | Amount/velocity checks, alert visualization |
| **Override/Cancel Operations** | 2 | Stripe refund integration, logging |
| **Transaction History & Filtering** | 2 | Search, filter, detail view |
| **Testing (Unit, Integration, E2E)** | 2 | Mock data, performance under load |

**Total**: 13 points (large story - consider splitting if needed)

---

## 7. MoSCoW Classification

**MOSCOW**: MUST  
**Rationale**: Store managers need visibility and control. Dashboard is essential for operational success and fraud prevention.

---

## 8. Assumption References

| Assumption ID | Statement | Impact on Story |
|---------------|-----------|-----------------|
| ASSUMPTION-006 | Store managers are available during operating hours | Dashboard requires manager presence to be useful |
| ASSUMPTION-011 | Store Manager has authority to override fraud rules | Override functionality depends on this |

---

## 9. Definition of Done

- [ ] Dashboard login working with authentication
- [ ] All AC scenarios passing (SC-009.1 through SC-009.8)
- [ ] Real-time transaction feed working (WebSocket)
- [ ] Fraud alerts displaying correctly and triggering appropriately
- [ ] Exit validation alerts integrated
- [ ] Override/cancel functionality end-to-end tested
- [ ] Transaction history searchable and filterable
- [ ] Performance tested: 50+ concurrent transactions visible without lag
- [ ] Unit tests for dashboard logic (>85% coverage)
- [ ] Integration tests with backend API
- [ ] Security review: Manager authentication verified
- [ ] QA sign-off received
- [ ] No blocking bugs

---

## Related BRD Requirements

| BRD Requirement | Details |
|-----------------|---------|
| FR-3.1 to FR-3.5 | Store Manager Dashboard Features |

