# Composite Price Aggregation

#### Overview <a href="#overview" id="overview"></a>

Igniz employs multiple independent pricing mechanisms that aggregate order book data and external market feeds to establish manipulation-resistant reference prices. This multi-source approach ensures accurate valuation across all trading operations while mitigating single-point-of-failure risks.

#### Reference Price Feed <a href="#reference-price-feed" id="reference-price-feed"></a>

The **Reference Price** serves as the primary input for funding rate calculations. This metric aggregates weighted median prices from multiple centralized exchange sources, providing complete independence from Igniz's internal order book data.

Reference price updates occur through validator consensus approximately every 3.5 seconds, ensuring timely price discovery while maintaining decentralization and tamper resistance. Because this feed relies exclusively on external market data, it remains immune to localized manipulation attempts on the Igniz platform.

#### Mark Price Composition <a href="#mark-price-composition" id="mark-price-composition"></a>

The **Mark Price** represents the platform's canonical fair value estimate for each perpetual contract. This price determines margin calculations, liquidation triggers, conditional order execution (take-profit/stop-loss), and unrealized profit/loss accounting.

Mark price derives from the median of three independent price sources:

**1. Adjusted Reference Price**

The reference price adjusted by a 180-second exponential moving average (EMA) of the basis spread between Igniz's mid-market price and the external reference price. This adjustment captures local supply-demand dynamics while anchoring to external markets.

**2. Platform Order Book Median**

The median value of three internal data points:

* Current best bid price
* Current best ask price
* Most recent executed trade price

This component reflects real-time trading activity on the Igniz order book.

**3. External Perpetuals Aggregate**

A weighted median of perpetual futures mid-market prices from major centralized exchanges:

* Binance: Weight 4
* Bybit: Weight 2
* OKX: Weight 2
* KuCoin: Weight 1
* HTX: Weight 1

Exchange selection prioritizes liquidity depth and operational reliability.

**Fallback Mechanism:**

When exactly two of the three primary inputs are available, the system incorporates a 40-second EMA of the platform's order book median (best bid, best ask, last trade) as an additional input to the median calculation. This ensures robust pricing even during temporary data source outages.

#### Update Frequency <a href="#update-frequency" id="update-frequency"></a>

Both reference and mark prices refresh synchronously with validator oracle publications, maintaining approximately 3.5-second update intervals. This frequency balances real-time responsiveness with computational efficiency across the distributed validator network.

#### Exponential Moving Average Specification <a href="#exponential-moving-average-specification" id="exponential-moving-average-specification"></a>

The platform employs a time-weighted exponential moving average to smooth price transitions and filter transient volatility. For each update sample at time interval `Δt` since the prior update:

```
smoothed_value = accumulator_numerator / accumulator_denominator
```

**Update Logic:**

```
accumulator_numerator = accumulator_numerator × e^(-Δt / 3.0 minutes) + sample_value × Δt

accumulator_denominator = accumulator_denominator × e^(-Δt / 3.0 minutes) + Δt
```

The decay constant of 3.0 minutes provides approximately 95% weight on data from the past 9 minutes, effectively filtering noise while remaining responsive to genuine price movements.

#### Manipulation Resistance <a href="#manipulation-resistance" id="manipulation-resistance"></a>

The composite architecture provides multiple layers of manipulation resistance:

1. **External Reference Independence:** Funding rates derive entirely from off-platform data
2. **Multi-Source Aggregation:** Mark price requires consensus across independent sources
3. **Median-Based Calculation:** Outlier prices cannot skew the final valuation
4. **Temporal Smoothing:** EMA components prevent flash manipulation from affecting margins or liquidations

This framework ensures that accurate, manipulation-resistant pricing underpins all risk management and settlement operations on the platform.
