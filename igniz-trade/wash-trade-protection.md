# Wash Trade Protection

#### Mechanism Overview <a href="#mechanism-overview" id="mechanism-overview"></a>

Igniz implements automatic wash trade protection to prevent accounts from executing orders against their own resting liquidity. When an aggressive order from a wallet address would match against passive liquidity from the same address, the platform automatically cancels the resting order rather than processing a self-execution.

This behavior occurs transparently without generating trade records, fee assessments, or public transaction logs.

#### Operational Behavior <a href="#operational-behavior" id="operational-behavior"></a>

When an incoming order encounters same-address liquidity on the opposite side of the order book:

1. The passive (resting) order cancels immediately
2. The aggressive (incoming) order continues processing against remaining book liquidity
3. No trading fees apply to the canceled passive order
4. The cancellation does not appear in public trade feeds or transaction history
5. The aggressive order may continue filling against other market participants' liquidity at deeper price levels

This automatic cancellation mechanism prevents circular trading activity while preserving the aggressive order's ability to access legitimate counterparty liquidity beyond the self-matched level.

#### Market Making Applications <a href="#market-making-applications" id="market-making-applications"></a>

Centralized exchanges commonly label this functionality as "Cancel Maker" or "Expire Resting" mode. The behavior proves particularly valuable for algorithmic market-making strategies where liquidity provision and liquidity consumption occur from the same account.

**Use Case Scenario:**

Market making algorithms frequently adjust bid/ask spreads in response to market conditions. When rebalancing positions, these algorithms may submit aggressive orders that would otherwise interact with their own previously-placed passive orders.

The wash trade protection mechanism allows aggressive rebalancing orders to:

* Automatically clear outdated same-address passive liquidity
* Continue executing against legitimate external counterparties
* Capture depth beyond the original passive order price level
* Maintain intended position adjustments without manual order management overhead

This automation reduces latency in high-frequency strategies and eliminates the need for pre-cancellation logic before submitting rebalancing orders.

#### Fee and Settlement Implications <a href="#fee-and-settlement-implications" id="fee-and-settlement-implications"></a>

Canceled passive orders under wash trade protection:

* Incur zero trading fees
* Do not consume rate limit allocations
* Do not affect realized profit/loss calculations
* Leave position acquisition prices unchanged

The aggressive order continues normal fee assessment and settlement processing for any fills against external counterparty liquidity.

#### Transparency and Compliance <a href="#transparency-and-compliance" id="transparency-and-compliance"></a>

While wash trade protection prevents self-execution, the platform maintains complete transparency regarding order flow and execution. All legitimate trades between distinct addresses appear in trade feeds with full attribution. The protection mechanism serves exclusively to prevent inadvertent self-trading rather than obscuring transaction activity.

This design aligns with regulatory frameworks prohibiting wash trading while supporting legitimate algorithmic trading strategies that require simultaneous maker and taker activity from unified capital pools.
