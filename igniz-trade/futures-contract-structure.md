# Futures Contract Structure

#### Overview <a href="#overview" id="overview"></a>

Perpetual futures on Igniz are derivative instruments with no expiration mechanism. Price alignment with underlying spot markets is maintained through periodic funding rate settlements rather than contract rollover.

#### Collateral and Denomination Model <a href="#collateral-and-denomination-model" id="collateral-and-denomination-model"></a>

Igniz perpetual contracts utilize a USDC-collateral model with USDT-denominated pricing for optimal liquidity access and capital efficiency. Oracle price feeds are quoted in USDT, while all margin and settlement operations process in USDC.

This architecture operates as a quanto structure where profit and loss calculated in USDT terms settles in USDC without exchange rate conversion. This design eliminates conversion friction while maintaining alignment with the most liquid pricing sources across centralized and decentralized markets.

For assets where the primary liquidity source denominates in USDC, oracle pricing follows the native denomination. This applies to select platform-native tokens and assets with concentrated USDC-pair liquidity.

#### Specification Parameters <a href="#specification-parameters" id="specification-parameters"></a>

Igniz maintains streamlined contract specifications with minimal asset-specific variations and no account-level restrictions. This standardized approach simplifies trading operations across all supported instruments.

| **Parameter**                  | **Specification**                                                                                                                                                 |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Contract Type                  | Linear perpetual futures                                                                                                                                          |
| Contract Size                  | 1 unit of base asset                                                                                                                                              |
| Price Reference                | Igniz composite oracle aggregating spot market data                                                                                                               |
| Initial Margin Requirement     | Inverse of selected leverage (e.g., 5% at 20x leverage)                                                                                                           |
| Maintenance Margin Requirement | 50% of initial margin at maximum leverage                                                                                                                         |
| Valuation Price                | Refer to robust price index documentation                                                                                                                         |
| Settlement Cycle               | Continuous (funding settlements every hour)                                                                                                                       |
| Position Size Limits           | None                                                                                                                                                              |
| Margin Mode                    | Cross-margin or isolated margin per wallet                                                                                                                        |
| Funding Baseline Notional      | 25000 USDC for BTC and ETH; 8000 USDC for alternative assets                                                                                                      |
| Market Order Ceiling           | 20,000,000forleverage≥30x;20,000,000forleverage≥30x;7,000,000 for leverage \[25, 30); 3,000,000forleverage\[15,25);3,000,000forleverage\[15,25);750,000 otherwise |
| Limit Order Ceiling            | 10× market order ceiling for corresponding leverage tier                                                                                                          |

#### Design Rationale <a href="#design-rationale" id="design-rationale"></a>

The standardized specification framework reduces complexity for traders while maintaining robust risk management across all market conditions. Uniform parameters enable consistent strategy deployment across multiple assets without requiring instrument-specific configuration adjustments.
