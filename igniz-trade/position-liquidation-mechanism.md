# Position Liquidation Mechanism

#### **Liquidation Trigger Conditions**

Position liquidation activates when adverse price movements reduce account equity below the maintenance margin threshold. The maintenance threshold equals 50% of the initial margin requirement at maximum permissible leverage, which varies from 3x to 50x across different assets.

Consequently, maintenance margin ranges from 1.0% (for 50x maximum leverage instruments) to 16.7% (for 3x maximum leverage instruments), depending on the specific asset's risk classification.

#### **Primary Liquidation Process**

When account equity breaches the maintenance threshold, the platform initiates an order book liquidation attempt. The system submits market orders equal to the full position size directly to the decentralized order book, allowing all market participants to provide counter-liquidity.

These liquidation orders may execute fully, partially, or not at all depending on available order book depth. If executed orders restore the account to compliance with maintenance requirements, any residual collateral remains with the position holder. This approach maximizes capital retention for traders facing liquidation.

#### **Secondary Liquidation Protocol**

If account equity deteriorates to 66% of the maintenance margin without successful order book liquidation, the platform triggers a secondary liquidation mechanism through the Insurance Vault system.

**Unified Collateral Liquidation:**

For accounts utilizing unified collateral mode, the entire account—including all positions and available margin—transfers to the Insurance Vault. Traders with no segregated positions will have zero remaining account equity following vault liquidation.

**Segregated Collateral Liquidation:**

For positions in segregated mode, only the specific position and its allocated margin transfer to the Insurance Vault. All unified collateral positions and other segregated positions remain unaffected by the liquidation event.

**Margin Forfeiture:**

During Insurance Vault liquidations, maintenance margin does not return to the position holder. This buffer ensures vault profitability across market conditions and compensates for execution risk during volatile periods.

Traders can avoid margin forfeiture by implementing stop-loss orders or manually closing positions before valuation price reaches the liquidation threshold.

#### **Valuation Price in Liquidations**

Liquidation calculations utilize the Valuation Price (composite price aggregating external exchange data with platform order book state) rather than instantaneous order book prices. This methodology provides manipulation resistance and prevents temporary price dislocations from triggering premature liquidations.

During elevated volatility or for highly leveraged positions, valuation price may diverge significantly from instantaneous book prices. Traders monitoring liquidation risk should reference the precise liquidation price formula for accurate threshold calculations.

#### **Design Philosophy**

The dual-stage liquidation architecture prioritizes capital preservation for distressed traders. By directing liquidation flow to the public order book first, Igniz enables competitive pricing from all market participants and maximizes residual value retention.

Unlike centralized exchanges, Igniz imposes no clearance fees on liquidations. The transparent, decentralized process ensures that liquidated traders retain maximum possible equity while maintaining platform solvency.

#### **Incremental Liquidation System**

For positions exceeding 125,000 USDC in notional value (15,000 USDC on testing environments), the initial liquidation order represents only 25% of the total position size. This incremental approach reduces market impact and provides opportunities for partial position recovery.

Following any block containing an incremental liquidation event, a 40-second stabilization period activates. During this interval, subsequent liquidation orders will target the complete remaining position rather than incremental portions.

#### **Insurance Vault Structure**

The Insurance Vault operates as a community-owned risk buffer integrated into the platform's liquidity provisioning system. Positions falling below 66% of maintenance margin transfer to the vault under backstop liquidation conditions.

Statistical analysis demonstrates that vault liquidations generate positive returns over time. On most trading venues, liquidation profits accrue to exchange operators or privileged market-making firms. Igniz directs all liquidation revenue streams to the community through the Insurance Vault's liquidity pool participants.

This democratized profit-sharing model ensures that platform participants, rather than centralized operators, benefit from liquidation activities.

#### **Liquidation Price Calculation**

**Entry Price Estimate:**

When initiating a trade, the platform displays an estimated liquidation price. This estimate may deviate from the actual liquidation threshold due to dynamic order book liquidity conditions and evolving market depth.

**Position Liquidation Price:**

Once a position opens, the displayed liquidation price incorporates the confirmed entry price but remains subject to adjustment from:

* Periodic funding rate settlements
* Unrealized profit/loss fluctuations in other positions (unified collateral mode only)

**Leverage Impact:**

For unified collateral positions, liquidation price remains independent of selected leverage. Lower leverage configurations simply allocate larger collateral amounts while maintaining identical liquidation thresholds.

For segregated positions, liquidation price directly depends on selected leverage, as allocated margin scales with the initial margin requirement determined by leverage selection.

**Insufficient Margin Scenarios:**

When available margin proves insufficient for the desired trade size, liquidation price calculations assume account funding to the minimum initial margin requirement.

**Precise Calculation Formula:**

```
liquidation_price = entry_price - position_direction × available_buffer / position_quantity / (1 - maintenance_factor × position_direction)
```

**Where:**

```
maintenance_factor = 1 / MAINTENANCE_LEVERAGE_THRESHOLD
```

For assets subject to progressive leverage tiers, the maintenance leverage value corresponds to the tier applicable at the liquidation price position notional.

```
position_direction = 1 for long positions, -1 for short positions

available_buffer (unified) = total_account_equity - aggregate_maintenance_requirement

available_buffer (segregated) = allocated_margin - position_maintenance_requirement
```

This formula provides precise liquidation price determination accounting for all margin mode configurations and leverage tier structures.
