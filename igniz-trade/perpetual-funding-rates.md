# Perpetual Funding Rates

#### Mechanism Overview <a href="#mechanism-overview" id="mechanism-overview"></a>

Funding rates constitute a periodic settlement mechanism that maintains price convergence between perpetual futures contracts and their underlying spot asset valuations. This system ensures that perpetual contract prices track spot market values over time despite lacking traditional expiration-based arbitrage mechanisms.

The funding mechanism operates as a direct peer-to-peer transfer between position holders on opposing sides of the market. Long position holders pay short holders when perpetual prices trade above spot references, while short holders compensate long holders when perpetual prices fall below spot valuations. Igniz collects no intermediary fees on these transfers—all payments flow directly between traders.

#### Rate Composition <a href="#rate-composition" id="rate-composition"></a>

The funding rate comprises two distinct components:

**Base Interest Component**

A fixed interest rate differential represents the cost-of-carry difference between holding USD-denominated collateral versus cryptocurrency spot positions. This component is standardized at 0.01% per 8-hour period, equivalent to 0.00125% hourly or approximately 10.95% annualized. This fixed component biases funding slightly toward short positions, reflecting typical borrowing cost differentials in traditional finance.

**Market Premium Component**

The premium component adjusts dynamically based on the spread between perpetual contract execution prices and external spot market reference prices. When perpetual contracts trade at a premium to spot (positive premium), the funding rate increases, requiring long holders to pay shorts. Conversely, when perpetuals trade at a discount (negative premium), the funding rate decreases or turns negative, requiring shorts to compensate longs.

This dynamic adjustment creates economic incentives for arbitrageurs to restore price equilibrium. Elevated funding rates attract traders to the cheaper side of the market, naturally correcting price dislocations.

#### Settlement Frequency <a href="#settlement-frequency" id="settlement-frequency"></a>

Funding settlements execute every hour on Igniz. At each interval, the applicable funding rate adjusts account balances of all position holders proportionally to their exposure and the direction of their positions.

This hourly settlement frequency provides continuous price anchoring while limiting the magnitude of individual funding payments compared to longer settlement intervals.

#### Calculation Methodology <a href="#calculation-methodology" id="calculation-methodology"></a>

The funding rate calculation aligns with institutional-grade perpetual exchange standards to ensure familiarity for professional traders.

**Base Formula:**

The 8-hour funding rate follows this formula, with hourly payments equal to one-eighth of the computed rate:

```
Funding_Rate (F) = Average_Premium (P) + clamp(base_interest - Premium (P), -0.0005, 0.0005)
```

The premium component samples every 4 seconds and averages across the hour-long measurement window.

**Premium Calculation:**

```
premium = impact_spread_differential / reference_price
```

**Where:**

```
impact_spread_differential = max(impact_bid - reference_price, 0) - max(reference_price - impact_ask, 0)
```

The `impact_bid` and `impact_ask` represent average execution prices for trading the designated impact notional quantity on respective order book sides. Impact notional values vary by asset as specified in the Futures Contract Structure documentation.

**Reference Price Source:**

Reference prices aggregate weighted median spot prices from major centralized exchanges, calculated independently by each validator node. Exchange weights reflect relative liquidity depth to ensure robust, manipulation-resistant pricing. This reference price methodology is detailed in the Composite Price Aggregation documentation.

#### Rate Constraints <a href="#rate-constraints" id="rate-constraints"></a>

Igniz implements a funding rate ceiling of 5% per hour, preventing extreme funding costs during periods of exceptional market imbalance. This constraint provides more conservative protection compared to typical centralized exchange implementations, ensuring that funding costs remain manageable even during volatile market conditions.

The rate ceiling and settlement interval apply uniformly across all instruments, maintaining consistent funding dynamics regardless of asset characteristics.

#### Payment Calculation <a href="#payment-calculation" id="payment-calculation"></a>

Funding payments at each settlement interval calculate as:

```
funding_payment = position_quantity × reference_price × funding_rate
```

**Critical Note:** The reference price (external spot oracle) converts position quantity to notional value for funding calculations, not the valuation price used for margining. This design prevents circular dependencies between funding calculations and margin requirements.

#### Practical Example <a href="#practical-example" id="practical-example"></a>

Consider this scenario to illustrate funding calculation:

**Given:**

1. Base interest rate: 0.01% (fixed constant)
2. Perpetual trading at premium: impact bid price = 10,200,spotreference=10,200,spotreference=10,000
3. Premium differential: $200
4. Settlement interval: 1 hour
5. Your position: 10 BTC long

**Step 1: Calculate Premium**

```
Premium = (Impact_Bid - Reference_Price) / Reference_Price
Premium = ($10,200 - $10,000) / $10,000
Premium = 0.02 (or 2%)
```

**Step 2: Apply Clamp Function**

```
Clamped_Adjustment = clamp(Base_Interest - Premium, -0.06%, 0.06%)
Clamped_Adjustment = clamp(0.01% - 2%, -0.06%, 0.06%)
Clamped_Adjustment = clamp(-1.99%, -0.06%, 0.06%)
Clamped_Adjustment = -0.06%
```

**Step 3: Determine Funding Rate**

```
Funding_Rate = Premium + Clamped_Adjustment
Funding_Rate = 2% + (-0.06%)
Funding_Rate = 1.94%
```

Since the funding rate is positive and you hold a long position, you would pay 1.94% of your notional position value to short position holders at the next settlement interval.

This elevated funding rate creates strong economic incentive for traders to open short positions or close long positions, naturally driving the perpetual price back toward the spot reference level.
