# Centrifuge - TWAMM

TWAMM - Time Weighted Automated Market Maker

:warning: NOTE:

:warning: Expect Breaking Changes

TWAMM provide a core lib for interacting with a Substrate based blockchain.
It enables its consumers to **submit/cancel/update long-term swaps of two assets based on the configured embedded AMM**.

* Uses [`pallet-asset-conversion`](https://github.com/paritytech/substrate/pull/12984) as the underlying embedded AMM in the default configuration
* Long-term orders are split into infinitely many infinitely small suborders for a given time interval
* Orders can be cancelled anytime
 
## Protocol

The TWAMM protocol represents a sophisticated advancement of the conventional constant product (Uniswap-v2 style) automated market maker (AMM) framework. It introduces a novel feature wherein users can split their orders into infinitely small fractions and execute them at each block interval. This feature addresses a significant shortcoming of traditional AMMs that encounter severe slippage issues when processing sizable orders, leaving them vulnerable to front-running tactics.

Furthermore, the inherent risk associated with executing transactions within a single block is nullified by the TWAMM protocol. By segmenting orders into hundreds of small units spread over a prolonged time frame, the slippage rate of the embedded AMM is markedly reduced, and the cost of price manipulation is exponentially elevated. Artificially manipulating prices over multiple blocks creates an opportunity for other traders to exploit the price-inflation tactic, which ultimately undermines the manipulative effort. This innovative solution offers an elegant means of executing large orders even in relatively illiquid markets. It is particularly well-suited for slow automated processes, such as governance-controlled treasury operations, that can effectively implement a dollar-cost averaging strategy over extended periods.

Based on [Paradigm Paper](https://www.paradigm.xyz/2021/07/twamm).

## Background

The [ongoing discussion](https://forum.polkadot.network/t/statemint-update-roadmap/1200/) about automated market makers (AMMs) in the Statemint roadmap is mainly focused on improving the user and custodian experience by allowing small atomic swaps natively and making Statemint a central hub for asset deposits. This addresses important pain points by enabling transaction fees to be paid in non-native tokens and facilitating interaction with the entire asset variety of the ecosystem without requiring to run custom nodes or infrastructure.

However, we believe that one aspect has been overlooked: the slow swaps of large volumes, which can be easily front-run or sandwiched. This is particularly relevant in the context of trades proposed through governance for use cases such as
1. Paying out treasury grants, bounties or even [salaries](https://github.com/paritytech/substrate/pull/13378) in less volatile currencies (ie. stablecoins).
2. Enabling parachains to build a DOT reserve which can be used to acquire a parachain lease, pay XCM fees, or [increase availability cores](https://forum.polkadot.network/t/parachain-scaling-by-parablock-splitting/341) during times of high demand (once supported).
3. Governance deciding to invest part of the treasury into a token to diversify their treasury

What we need is the opposite of what a traditional AMM provides: atomic swaps with immediate execution, even in relatively illiquid assets. A governance vote is unable to time the market and is highly predictable. Therefore, executing such a transaction as a market order on an AMM is problematic, as it will be guaranteed to be front-run. A better solution is to "dollar-cost-average" over a long period of time making it harder for price manipulation to affect the purchase.