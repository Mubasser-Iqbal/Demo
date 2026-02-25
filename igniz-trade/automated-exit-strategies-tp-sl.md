# Automated Exit Strategies (TP/SL)

#### Overview <a href="#overview" id="overview"></a>

Automated profit capture and loss mitigation orders enable traders to establish predetermined exit points that execute without manual intervention. These conditional orders close positions when market prices reach specified thresholds, systematically realizing gains or limiting downside exposure.

#### Trigger Price Mechanism <a href="#trigger-price-mechanism" id="trigger-price-mechanism"></a>

Both profit capture and loss mitigation orders utilize the **Valuation Price** (the platform's composite manipulation-resistant price index) as the activation reference. This methodology prevents premature triggering from temporary order book dislocations or localized price manipulation attempts.

For detailed information on Valuation Price composition, refer to the Composite Price Aggregation documentation.

#### Chart-Based Order Management <a href="#chart-based-order-management" id="chart-based-order-management"></a>

The integrated TradingView charting interface supports visual order manipulation through drag-and-drop positioning. Traders can adjust trigger levels directly on price charts by repositioning order markers to desired price thresholds.

**Immediate Execution Prevention:**

The platform rejects repositioning actions that would cause instant order activation (e.g., dragging a profit target below current market price for long positions). This safeguard prevents inadvertent immediate execution resulting from imprecise visual placement.

Traders intending immediate position closure should utilize the position management panel or standard order entry interface rather than conditional order repositioning.

#### Execution Type Selection <a href="#execution-type-selection" id="execution-type-selection"></a>

Automated exit orders support both market and limit execution modes, enabling traders to balance execution certainty against slippage control.

**Market Execution Mode:**

Profit and loss orders configured for market execution activate as immediate market orders upon trigger. These orders incorporate a default slippage tolerance of 12%, preventing execution at severely adverse prices during volatile conditions.

Market mode prioritizes execution certainty over price precision, ensuring position closure even during rapid price movements or reduced liquidity environments.

**Limit Execution Mode:**

Limit-configured exit orders activate as limit orders at the trader-specified limit price upon trigger. This approach provides precise slippage control but introduces fill uncertainty if market price moves beyond the limit threshold before execution.

**Execution Trade-offs:**

Aggressive limit prices (closer to trigger price) increase fill probability but allow greater slippage. Conservative limit prices (further from trigger) reduce maximum slippage but risk non-execution if price continues moving unfavorably.

**Practical Illustration:**

Consider a loss mitigation order protecting a long position:

* Trigger threshold: $10.00
* Limit price: $10.00

When valuation price declines below 10.00,theorderactivatesandrestsasalimitsellat10.00,theorderactivatesandrestsasalimitsellat10.00. If price gaps from 11.00to11.00to9.00 instantaneously, the order likely rests unfilled at $10.00 rather than executing.

Alternatively, setting the limit price to 8.50increasesexecutionprobability.Theorderwouldlikelyfillsomewherebetween8.50increasesexecutionprobability.Theorderwouldlikelyfillsomewherebetween9.00 and $8.50, providing position closure with controlled maximum slippage.

#### Position-Associated Exit Orders <a href="#position-associated-exit-orders" id="position-associated-exit-orders"></a>

Exit orders created from the position management interface default to full position sizing, automatically adjusting order quantity to match current position size at placement.

**Dynamic vs. Fixed Sizing:**

* **Default behavior:** Order quantity equals total position size at creation
* **Custom sizing:** Specifying explicit quantity creates fixed-size orders that do not adjust if the underlying position changes

Position-associated orders provide simplified management suitable for basic exit automation. These orders activate based on position-level triggers and cancel automatically upon position closure through other means.

#### Parent Order Contingent Exits (One-Cancels-Other Logic) <a href="#parent-order-contingent-exits-one-cancels-other-logic" id="parent-order-contingent-exits-one-cancels-other-logic"></a>

Advanced traders can attach exit orders to entry orders, creating contingent execution relationships where profit/loss orders activate only under specific parent order conditions.

**Operational Mechanics:**

Exit orders created from the order entry form link to their parent order with fixed sizing equal to the parent order quantity.

**Activation Conditions:**

**1. Immediate Placement (Full Fill at Entry):**

When the parent order fills completely upon initial placement, attached exit orders activate immediately. This behavior mirrors position-associated exit orders.

**2. Dormant State (Partial or No Fill):**

When parent orders rest unfilled or partially filled, attached exit orders enter a dormant state. These orders have not been submitted to the conditional order system.

**3. Cancelation Behavior:**

* **Manual cancelation of unfilled parent:** Exit orders cancel completely
* **Manual cancelation of partially-filled parent:** _**Exit orders cancel entirely, even for the filled portion**_
* **Insufficient margin auto-cancelation:** Exit orders activate for the filled portion

**Critical Constraint:**

Traders canceling partially-filled parent orders must manually establish new exit protection for the executed quantity. The platform does not automatically preserve exit orders when parent orders cancel with partial fills.

**Activation Rule:**

_**Contingent exit orders activate if and only if: (1) the parent order fills completely, OR (2) the parent order partially fills followed by automatic cancelation due to margin deficiency.**_

#### Use Case Recommendations <a href="#use-case-recommendations" id="use-case-recommendations"></a>

**Position-Associated Orders:**

* Suitable for straightforward exit automation on existing positions
* Ideal for traders prioritizing simplicity over complex order relationships
* Automatically manage full position exits without manual quantity tracking

**Parent-Contingent Orders:**

* Enable sophisticated entry/exit strategy execution in single order submission
* Appropriate for advanced traders comfortable with contingent order logic
* Require careful management of partial fill scenarios to avoid unprotected positions

Both approaches provide powerful automation capabilities when applied to appropriate trading contexts.
