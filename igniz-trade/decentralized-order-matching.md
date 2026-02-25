# Decentralized Order Matching

#### Order Book Architecture <a href="#order-book-architecture" id="order-book-architecture"></a>

Igniz maintains a native on-chain order book for each trading instrument, operating with mechanics analogous to institutional centralized exchange infrastructure while preserving full decentralization and transparency. All order placement, modification, and execution activities occur directly on the blockchain with immutable transaction records.

**Price and Quantity Discretization:**

Orders must conform to instrument-specific precision parameters:

* **Price levels:** Prices must align to integer multiples of the minimum tick size for each asset
* **Order quantities:** Sizes must conform to integer multiples of the minimum lot size for each asset

These discretization requirements ensure standardized price discovery and prevent order book fragmentation across infinitesimal price differentials.

**Matching Priority:**

The order matching engine processes orders according to strict price-time priority:

1. **Price priority:** Orders at superior price levels (higher for bids, lower for asks) execute first
2. **Time priority:** Among orders at identical price levels, earlier submissions receive execution priority

This priority mechanism mirrors traditional exchange matching logic, ensuring predictable and fair execution sequencing.

#### Margin Validation Integration <a href="#margin-validation-integration" id="margin-validation-integration"></a>

For perpetual futures instruments, order book operations integrate directly with the margin management system to enforce real-time collateral requirements. Margin validations occur at two critical junctures:

**1. Order Submission Validation:**

When traders submit new orders, the system immediately verifies that available collateral satisfies the margin requirement for the potential position increase. Orders exceeding available margin capacity reject at submission without entering the order book.

**2. Resting Order Revalidation:**

At order matching execution, the system revalidates margin adequacy for the passive (resting) order counterparty. This secondary validation ensures that reference price fluctuations occurring between order placement and execution do not create undercollateralized positions.

This dual-validation architecture maintains margin system consistency despite continuous reference price updates from external oracle feeds.

#### Transaction Ordering Logic <a href="#transaction-ordering-logic" id="transaction-ordering-logic"></a>

Igniz's consensus layer implements semantic awareness of order book interactions, enabling intelligent transaction sequencing within each block. This design optimizes execution fairness and prevents certain front-running vectors inherent in naive transaction ordering.

**Intra-Block Transaction Prioritization:**

Within each confirmed block, validated transactions execute in the following sequence:

**Priority Tier 1: Non-Aggressive Actions**

* Transactions containing no Good-Till-Cancel (GTC) or Immediate-Or-Cancel (IOC) orders
* Examples: account operations, transfers, query requests

**Priority Tier 2: Order Cancellations**

* All order cancellation requests process before new aggressive orders
* This sequencing prevents canceled orders from inadvertently matching against incoming market orders

**Priority Tier 3: Aggressive Order Placement**

* Transactions submitting at least one GTC or IOC order
* These potentially match against existing book liquidity

Within each priority tier, transactions execute in the sequence proposed by the block validator. Order modification operations classify according to the new order parameters being placed.

This semantic ordering framework ensures that cancellations always precede new order matching within the same block, eliminating race conditions where traders attempt to cancel orders simultaneously with potential matching counterparties submitting aggressive orders.

#### Decentralization and Transparency <a href="#decentralization-and-transparency" id="decentralization-and-transparency"></a>

Unlike traditional exchange order books operating in opaque private infrastructure, Igniz's order book state exists entirely on-chain. All participants can independently verify:

* Complete order book depth at any historical block
* Precise matching logic for every executed trade
* Margin validation results for every order submission

This transparency eliminates information asymmetry between exchange operators and market participants, creating a level playing field where all traders access identical market state information simultaneously.
