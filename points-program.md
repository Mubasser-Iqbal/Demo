# Points Program

#### Overview

Points reward users who contribute to platform growth and liquidity. The program operates in seasonal phases with weekly distributions every Friday, covering activity from Wednesday through Tuesday (UTC). Igniz reserves full discretion to modify distribution criteria.

***

#### Trader Points

Points are earned across multiple dimensions with non-linear scaling that prioritizes quality over quantity:

**Core Metrics:**

* Volume (weighted by market)
* Open Interest
* Funding Participation
* Liquidations & Deleveraging
* PnL Generation

**Key Principles:**

* Premium accounts receive enhanced weighting on certain metrics
* Markets have different weights based on liquidity and strategic importance
* Doubling a metric doesn't double points—may result in 1.5× to 3× depending on quality
* Some metrics evaluated daily, others weekly
* Intentional losses or liquidation farming provide no benefit

#### Referral System

Referrers earn derivative points based on referred users' activity throughout their lifecycle. The multiplier incentivizes quality user acquisition while maintaining fair distribution across all participants.

***

#### Market Maker Points

Premium accounts only. Separate allocation from trader points.

#### Volume-Based

Maker volume earns points with progressive bonuses beyond specified thresholds. Volume above baseline levels receives enhanced weighting, with bonus multipliers increasing at higher tiers. Volume evaluated on full weekly cycle. All markets weighted equally for volume.

#### Liquidity Provision

Order books sampled at random intervals. Points distributed based on proportional liquidity share across:

* **Depth thresholds**: liquidity within top $X notional bands
* **Spread tightness**: liquidity within X bps of mid-price
* **Evaluated separately for bid/ask sides**

**Market Weighting:** Markets dynamically weighted based on:

* Cross-exchange volume comparisons
* Open interest across venues
* Local vs. external market maturity
* Weights recalculated hourly

**Volatility Multiplier:** Snapshots during volatile periods receive higher weight to incentivize liquidity provision when capital is at risk. Daily ceiling mechanisms prevent single volatile days from dominating distributions.

#### Protocol Liquidity

Platform-owned liquidity (automated pools, treasury MM) operates under identical rules but receives no direct allocations. Points attributed to protocol liquidity are redistributed proportionally across all qualifying participants.

***

#### Anti-Gaming

**Multi-Account Allowance:** Up to a defined threshold permitted for legitimate risk management. Beyond this, detection systems evaluate account clusters.

**Detection Methods:**

* Automated pattern recognition
* Entity clustering via on-chain analysis
* Wash trading and circular flow detection
* Cross-account correlation analysis

Specific parameters undisclosed. Wash trading, self-trading, deliberate loss generation, and coordinated farming violate program integrity and may result in retroactive nullification.

***

**Disclaimer:** Igniz maintains complete authority over all parameters. Rules may change at any time. Allocations resulting from exploitative behavior may be reversed.
