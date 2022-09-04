# diff-debt-pools

**Diff debt pool**

Swapping syX DPS to syY DPS means reducing debt pool and associated
collateral from X debt pool to increase Y debt pool.

1.  When a user enters the system he is assigned with
    > $\text{TheoreticalDPSMax}$ based on the composition of the global
    > debt pool. The formula to compute $\text{TheoreticalDPSMax}$ is:

$TheoreticalDPSMax(j) = \frac{Collateral(j)}{PriceSyAsset(j) \cdot \ Min.CollateralRatio(j)}$

> When the user decides to open debitory position, the system transforms
> $\text{TheoreticalDPSMax}$ in $\text{DPS}$, reducing the amount of
> $\text{TheoreticalDPSMax}$ while increasing the $\text{DPS}$. Because
> of the design $\text{TheoreticalDPSMax} \geq \text{DPS}$, (indeed
> $TheoreticalDPSAvailable = \ TheoreticalDPSMax - DPS$).

2.  Users individual debt is calculated as:

$Debt(j) = DPS(j)\  \cdot \ DPSPrice(j),\ with\ DPSPrice = syAssetPrice(j)$

> Individual debt of a specific Debt Pool (DP) increases in one (or
> both) of the circumstances below:

a.  $DPSPrice(j)$ increases because $syAssetPrice(j)$ increases.

b.  User takes more debt, increasing the amount of $DPS(j)$ employed
    > (i.e. reducing the amount of $\text{TheoreticalDPS}$ he is
    > assigned to).


3.  When there is a swap between DPSs of different DPs, what changes is
    > not the price of the individual DPS(j) but the quantity of DPS
    > transferred. Since individual user debt is affected by quantity of
    > DPS, applying a spread ($\Delta$) to DPS quantity transferred in
    > every swap incentivises users virtuous behaviors. Indeed, with the
    > $\Delta$, transferring DPS from DP with low C-Ratio to DP with
    > high C-Ratio should be incentivized, while the opposite should be
    > disincentivized. Calculating $\Delta$ implies using C-Ratios.

> C-Ratio takes into account both collateral and debt in a specific DP
> and is computed based on the formula below:

$C - RatioDP(j)\  = \ \frac{CollateralValue(j)}{DebtValue(j)}$

With $C - RatioDP(j)$ defined, $\Delta$ is computed as:

$\Delta = \frac{\ C - RatioDP(j)\  - \ C - RatioDP(i)}{100}$

Given $\Delta$ and $C - RatioDP$ defined, the exchange rate between DPSs
is the following:

$dDPS(j/i) = \frac{DPS(j)Price}{DPS(i)Price} \cdot \ (1 - \Delta)$

> If $\Delta$ is positive, the exchange rate is lower than fair rate (it
> is good for the user because the lower the quantity of DPS the lower
> the debitory position in place and the higher the
> $\text{TheoreticalDPSAvailable}$ for a specific DP, it implies an
> instant arbitrage profit) on the opposite side if $\Delta$ is negative
> the exchange rate for the trader is higher (instant loss) than the
> fair price. The $\Delta$ is introduced to incentivize balance between
> debt pools. From a general perspective it is always cheaper exchanging
> DPS from a pool with high C-Ratio to a pool with a lower one.
> Obviously, the opposite is always true too.
>
> User A decides to exchange part of its DPS from a pool with high
> C-Ratio to a pool with a lower C-Ratio realizes an immediate gain from
> the trade thanks to the $\Delta$ applied in the exchange rate. Indeed,
> collateral quantity is exchanged from pools at a 1:1 exchange rate,
> while the DPS exchange rate (i.e. the debt quantity since debt is
> equal to DPS Quantity \* syAssetPrice) is subjected to $\Delta$
> discount. At the end of the trade, User A, will have the same
> collateral deposited (shifted from syX debt pool to syY debt pool)
> with less quantity of DPS(debt), resulting in a better individual
> C-Ratio and an immediate profit.

Example

[[DPS DIff token pool model
raw.pdf]{.ul}](https://drive.google.com/file/d/1-UfxustesZvExcEnLqb6zJ47DZtQfb5f/view?usp=sharing)

**DPS trade limits**

Users are allowed to exchange their DPS within certain limits, in order
to avoid voluntary liquidations and debt structure issues. Indeed, every
DPS can be exchanged until the C-Ratio of a specific pool does not reach
130%. It is not allowed to exchange DPS, and thus collateral, from a
pool with a global C-Ratio below 130%, conversely transferring DPS and
collateral from a pool with a C-Ratio bigger than 130% to a pool with a
130% C-Ratio is always possible. If the DPS transfer is big enough to
bring the C-Ratio of a DP below 130%, the trade can be executed
partially until the reach of the 130% C-Ratio limit.

Setting at 130% the threshold guarantees a buffer of safety in case of
low liquidity and high volatility since the liquidation threshold of
every pool is set at 120% C-Ratio.

**Debt Tracking**

User's debt balance is computed by taking into account each DPS(j)
multiplied for their prices. The constraint here is to always have a
debitory position above (1.2) 120%
$GlobalC - Ratio\  = \ \frac{CollateralValue(j)}{DebtValue(j)}$

**Example**

User A: Collateral deposited 500 USDT.

Debitory position: 50 DPS syETH and 30 DPS syBTC with ETH price of 1\$
and BTC price of 5\$.

Total debt: 50\*1+30\*5=200\$

Global C-Ratio:
$Global\ C - Ratio = \ \frac{500}{200}\  \rightarrow \ 250\%\ (2.5)$ .
The position is safe, alerts could be set by the user for certain Global
C-Ratio thresholds since the condition to be liquidated is to have a
GC-Ratio120%. If a single position is at risk of liquidation, an
automatic transfer of collateral is done from the highest C-Ratio pool
to the lowest. In this process, there is a transfer of thDPS from the
high C-Ratio DP to the low C-Ratio DP. thDPS becomes immediately DPS
since the isolated C-Ratio is close to 120%.

**Example**

Liquidation C-Ratio:120%

#ETH Position: 

ETH-POS = {Collateral : 100 USDT,

DPS-ETH: 83.3,

thDPSETH : 0,

ETH Price : 1\$ ,

ETH C-Ratio :  120%\}

#BTC position: 


BTC-POS = {Collateral:100 USDT,

DPSBTC : 2,

thDPSBTC : 1.33,

BTC Price : 20$ ,

BTCC-Ratio : 250%\}

At this point, there is a rebalance in individual collateral and DPS. A
portion of thDPSBTC is transferred to thDPSETH and then thDPSETH is
immediately converted into DPSETH to increase the ETHC-Ratio.
Rebalancing mechanisms follow the same formula computed in the Diff.
debt pool section. GlobalC-Ratio of the user is not affected by
individual rebalancing operations that work in the background. Since
BTCC-Ratio is higher than ETHC-Ratio the spread is positive and there is
a small profit for the user. If every individual position is 120% of the
user is at 120% C-Ratio, rebalancing operations are not possible and
liquidations occur.

Liquidations are split into equal portions.

Example:

User A has BTC and ETH positions with a C-Ratio of 120% thus his
GC-Ratio is 120%. Then there is a rise in debt value. GC-Ratio becomes
110%. The collateral is liquidated to get GC-Ratio again over 120% and
the liquidation is split 50/50 on BTC and ETH.
