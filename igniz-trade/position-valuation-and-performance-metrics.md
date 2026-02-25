# Position Valuation and Performance Metrics

#### Accounting Framework <a href="#accounting-framework" id="accounting-framework"></a>

Igniz's core settlement architecture operates on margin-based accounting for perpetual futures and balance-based accounting for spot assets. Position acquisition prices, unrealized performance, and realized performance metrics represent interface-level convenience calculations derived from underlying trade and margin data rather than fundamental blockchain state.

These derived metrics provide traders with intuitive performance tracking without affecting the underlying settlement logic or smart contract state transitions.

#### Perpetual Futures Accounting <a href="#perpetual-futures-accounting" id="perpetual-futures-accounting"></a>

**Position Direction Classification:**

Trades categorize as position-expanding or position-reducing based on their impact on absolute position magnitude:

* **Expanding trades:** Increase position size in the current direction (adding to long positions while long, or adding to short positions while short)
* **Reducing trades:** Decrease position size or reverse direction (reducing longs, reducing shorts, or flipping between long and short)

**Acquisition Price Adjustment:**

For position-expanding trades, the acquisition price recalculates as a size-weighted average incorporating both the existing position and the new trade:

```
updated_acquisition_price = (current_acquisition_price × existing_quantity + execution_price × trade_quantity) / (existing_quantity + trade_quantity)
```

For position-reducing trades, the acquisition price remains unchanged, preserving the original cost basis for performance calculation purposes.

**Unrealized Performance Calculation:**

Unrealized profit or loss reflects the current mark-to-market value of open positions:

```
unrealized_pnl = position_direction × (valuation_price - acquisition_price) × position_quantity
```

Where `position_direction = 1` for long exposures and `position_direction = -1` for short exposures.

This formula calculates the theoretical profit or loss if the position were closed at the current valuation price.

**Realized Performance Calculation:**

For position-reducing trades, realized performance captures the actual profit or loss crystallized through the closure:

```
realized_pnl = trading_fee + position_direction × (execution_price - acquisition_price) × closed_quantity
```

For position-expanding trades, realized performance consists solely of the trading fee (a negative value representing the cost of execution):

```
realized_pnl = trading_fee
```

This distinction ensures that performance attribution correctly separates costs associated with position establishment from profits/losses generated upon position closure.

#### Spot Asset Accounting <a href="#spot-asset-accounting" id="spot-asset-accounting"></a>

Spot trading applies equivalent performance formulas with directional classifications adapted to spot market mechanics.

**Trade Direction Classification:**

* **Position-expanding:** All purchase transactions (increases holdings)
* **Position-reducing:** All sale transactions (decreases holdings)

**Transfer and Distribution Treatment:**

Asset transfers between accounts process as synthetic buy or sell transactions valued at current valuation price. This methodology ensures consistent cost basis tracking across all balance changes.

Genesis token distributions (initial allocations from token launches) assign an acquisition price based on a standardized market capitalization of 12,500 USDC. This convention prevents undefined return calculations that would result from zero-cost basis, while acknowledging that genesis distributions represent allocations rather than purchases.

**Pre-Migration Balance Handling:**

Spot balances existing prior to the performance tracking feature activation (approximately July 8, 2024 at 12:00 UTC) receive an acquisition price equal to the valuation price at the first trade or transfer occurring after feature deployment.

This initialization approach provides reasonable performance baseline for historical holdings without requiring retroactive transaction history reconstruction.

#### Performance Metric Applications <a href="#performance-metric-applications" id="performance-metric-applications"></a>

These derived performance metrics serve multiple interface and analytical purposes:

* Portfolio performance dashboards and reporting
* Tax accounting and cost basis tracking
* Strategy performance evaluation
* Risk-adjusted return calculations

While these metrics do not affect margin calculations, liquidation logic, or settlement mechanics, they provide essential transparency for informed trading decisions and portfolio management.

Traders utilizing API integrations or custom interfaces can reconstruct these metrics using the documented formulas, ensuring consistency with platform-displayed values.
