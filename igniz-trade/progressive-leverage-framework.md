# Progressive Leverage Framework

#### Calculation Methodology <a href="#calculation-methodology" id="calculation-methodology"></a>

Igniz implements a progressive leverage system aligned with institutional exchange standards, where permissible leverage decreases as position size increases. This framework mitigates systemic risk from concentrated large positions while maximizing capital efficiency for smaller allocations.

The maintenance margin requirement follows a tiered formula:

```
maintenance_requirement = position_notional × tier_rate - tier_adjustment
```

Both `tier_rate` and `tier_adjustment` are determined by position size brackets rather than specific assets, creating uniform risk parameters across all instruments within each tier.

**Tier Rate Calculation:**

The maintenance rate at each tier derives from the maximum leverage available at that tier:

```
tier_rate(level = n) = (initial_margin_at_max_leverage_level_n) / 2
```

For example, at a tier offering 25x maximum leverage, the maintenance rate equals 2.0% (calculated as 4% initial margin / 2).

**Adjustment Factor:**

The tier adjustment factor ensures smooth transitions between leverage brackets, preventing discontinuous margin jumps at tier boundaries:

```
tier_adjustment(level = 0) = 0

tier_adjustment(level = n) = tier_adjustment(level = n - 1) +
    threshold_notional(level = n) × (tier_rate(level = n) - tier_rate(level = n - 1))
```

This adjustment mechanism ensures that:

1. Incremental position growth incurs margin costs at the current tier's rate
2. Total maintenance margin scales continuously across tier boundaries
3. No sudden margin spikes occur when crossing threshold values

**Framework Identification:**

Each leverage framework is assigned a unique identifier. Tier specifications are accessible via the platform's `meta` endpoint. For framework IDs below 50, a single-tier structure applies with maximum leverage equal to the ID value.
