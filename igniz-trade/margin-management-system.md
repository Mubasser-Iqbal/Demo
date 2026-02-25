# Margin Management System

#### **Overview**

Igniz implements margin calculation methodologies aligned with institutional-grade derivatives exchanges, providing traders with familiar risk management frameworks and capital allocation strategies.

#### **Collateral Allocation Modes**

Traders select their preferred collateral management approach when initiating positions. The platform supports multiple allocation modes to accommodate diverse trading strategies and risk preferences.

**Unified Collateral Mode**

The default configuration enables unified collateral allocation across all positions utilizing this mode. This approach maximizes capital efficiency by creating a shared collateral pool that supports multiple concurrent positions. All positions operating under unified collateral share both margin and profit/loss exposure, allowing positive performance in one market to offset requirements in another.

**Segregated Collateral Mode**

Segregated allocation constrains collateral to individual positions, creating isolated risk compartments. Under this model, liquidation events in one asset position do not impact other segregated positions or any positions utilizing unified collateral. Conversely, liquidations in unified mode or other segregated positions do not affect a specific segregated position.

Traders can adjust collateral allocation for segregated positions post-initialization, enabling dynamic risk management as market conditions evolve.

**Restricted Segregation Mode**

Certain assets operate under restricted segregation parameters, functioning identically to standard segregated mode with an additional constraint: collateral cannot be manually withdrawn. Instead, collateral releases proportionally as the position decreases, ensuring adequate margin coverage throughout the position lifecycle.

#### **Leverage Configuration and Initial Requirements**

Traders configure leverage as any integer value from 1x up to the asset-specific maximum threshold. Maximum permissible leverage varies by instrument based on liquidity characteristics and volatility profiles.

**Position Opening Requirements:**

The collateral requirement to establish a position calculates as:

```
required_collateral = (position_notional * reference_price) / selected_leverage
```

For unified collateral positions, allocated margin becomes locked and unavailable for withdrawal. Segregated positions permit margin adjustment after establishment.

Unrealized profit/loss treatment differs between modes:

* **Unified mode:**&#x55;nrealized gains immediately become available as margin for additional positions
* **Segregated mode:**&#x55;nrealized gains apply as supplementary margin to the existing position

**Leverage Adjustments:**

Existing positions support leverage increases without requiring position closure. The platform validates leverage only at position initialization. Subsequently, traders must monitor their leverage utilization ratio to maintain adequate margin buffers and prevent liquidation triggers.

For positions experiencing unrealized losses, available mitigation strategies include:

* Partial or complete position closure
* Margin addition (segregated positions)
* USDC collateral deposits (unified positions)

#### **Profit Extraction and Transfer Constraints**

Unrealized gains can be withdrawn from either segregated positions or unified accounts, subject to minimum margin retention requirements. The remaining margin must satisfy both:

1. Initial margin requirements for all open positions
2. A minimum threshold of 10% of aggregate notional exposure

The binding constraint determines the minimum transferable margin:

```
minimum_retained_margin = max(initial_margin_total, 0.1 × aggregate_position_notional)
```

Transfer operations encompass any margin extraction mechanism excluding direct trading activity. Examples include:

* Platform withdrawals to external wallets
* Internal transfers to spot trading accounts
* Margin reallocation between segregated positions

#### **Liquidation Thresholds**

**Unified Collateral Liquidation:**

Unified positions face liquidation when total account equity (including unrealized profit/loss) falls below the maintenance margin requirement multiplied by total position notional. The maintenance threshold is established at 50% of the initial margin requirement at maximum leverage.

**Segregated Collateral Liquidation:**

Segregated positions apply identical liquidation logic, but calculations isolate to the specific position's margin allocation and notional value, independent of other account positions or activities.

This bifurcated approach ensures that traders utilizing segregated mode maintain complete risk isolation, while unified mode participants benefit from portfolio-level margin efficiency.
