# electricity-market-microstructures
Working notes providing motitvation, and a high-level description of, an embedded domain specific language (EDSL) for modeling market microstructure of electricity markets.

Primary goal when designing an energy auction is to achieve the lowest-cost dispatch of generated electricity that balances supply and demand whilst minimizing transmission congestion and system contingency management costs. Auction mechanisms should be open, transparent, and competitive. 

Key question of interest:

1. To what extent can Blockchain technologies be leveraged to achieve openness and transparency of energy auction/settlement processes at minimal cost?  
2. Implicit collusion is a problem in many real world energy auctions.  How can we design energy auction mechanisms that minimize the potential for collusive behavior on the part of market participants without sacrificing too much allocative efficiency?

### Why care about electricity auction design?
Energy, particularly, electricity is a fundamentally scarce commodity that should efficiently allocated to its most productive uses.

The efficiency of an auction is determined by both the rules (i.e., activity rules or activity protocols) of the auction as well as the behavior of agents participating in the auction. To some extent agent behavior will be influenced by the rules governing the auction, but in general agent behavior will not be uniquely determined solely by the rules of the auction.  The API needs to express not only the rules of the auction, but also rules for agent behavior.

Some generic classes of auction rules:

1. Which agents (or types of agents?) can participate in an auction?
2. What kind of ask or bid orders are acceptable? In particular, do ask or bid orders need to satisfy a particular threshold (in terms of either price or quantity) in order to be acceptable?
3. Can aks or bid orders be changed once they are submitted?
4. How large are the ask or bid increments (in terms of both price and quantity)?
5. Auction timing: does the auction have one round? Multiple rounds? If multiple rounds, then when does one round in and another begin?

Effective auction designs encourage efficiency (both short and long-term) by disincentiving attempts by market participants to game the auction.  Good auction designs incentive market participants to submit ask or bid orders that truthfully reflect their respective preferences. Implementations of auction mechanisms that have lower entry barriers, reduce transaction costs (blockchain!), decrease uncertainty about likelihood of winning will also increase efficiency by increasing competitiveness.  Want to make it easy for lots of small players (i.e., participants with limited financial resources) to participate.

## Bilateral trading
Buyers and sellers pair up with one another (i.e., match!) and the reach an agreement (i.e., bargain!) regarding the terms of exchange. Important feature of bilateral energy markets is the continuous process of trading at prices which are typically unique to each transaction.

Individual buyers and sellers can agree to specialized forward contractsin order to guarantee delivery of specific quantities of power at certain points in the future. What role could blockchain-based smart contracts, such as Ethereum, play in forward electricity markets?

## Intermediated trading
Buyers and sellers can interact indirectly via some intermediary (i.e., some centralized market mechanism). Important feature of mediated energy markets (i.e., [power pools](https://en.wikipedia.org/wiki/Power_pool) and power exchanges) typically have a uniform price at which all buyers pay and all sellers receive.  Such auctions are typically run at regular intervals in order to set a market price ahead of physical delivery.

Standard (i.e., regulated) futures contracts can be used to specify delivery of specific quantities of power at certain points in the future. What role could blockchain-based smart contracts, such as Ethereum, play in markets for electricity futures?

## Intra-day electricity markets
Short-term electricity markets tend to operate on time horizons of one-day down to minutes prior to electricity being dispatched from specific generators.  Such markets are typically organized as mediated markets such as pools and exchanges run via auction mechanisms. As delivery time draws near opportunity cost of searching for a bilateral trading partner increases and factors influencing production and consumption decisions become more certain. These considerations tend to encourage market participants to use mediated markets.  To what extent will blockchain technology and "smart houses" allow for more "peer-to-peer" bilateral market forms to operate intra-day?

## Auction basics
Four basic types of auctions formats:

1. ascending-bid auction: (sometimes called the open, oral, or English auction)
2. descending bid auction: (sometimes called the Dutch auction)
3. first-price sealed bid auction
4. second-price sealed bid auction (sometimes called the Vickery auction)

To the above I would add double auctions as well as reverse auctions as variants/compositions of the above basic formats.

All four basic auction forms can lead to a uniform winning price when a single, unique object or good is auctioned.  However, energy auctions almost always involve the sale of multiple, _indistinguishable_ units (typically shares of load measured in MWh for a particular hour in the day, or share of generation capacity measured in MW to supply backup or replacement reserves).  In multi-unit auctions an alternative to uniform pricing is to use pay-as-bid pricing where all winning bidders pays (or all sellers receive) their respective limit prices.

### Revenue equivalence theorem
The [Revenue Equivalence Theorem](https://en.wikipedia.org/wiki/Revenue_equivalence) states that, under certain assumptions, the expected revenue generated via any of the above auction formats is the same.  However, as always when applying such theorems, the devil is in the details.  Apart from the technical assumptions, revenue equivalence requires market participants are risk-neutral with respect to money (i.e., have quasi-linear preferences) and have independent valuations of the good being auctioned.  Revenue equivalence fails under risk aversion or interdependent valuations.

### Bidding language
Bidding language needs to be sufficiently flexible to allow market participants to express their preferences.  From [Chao and Wilson (2002)](http://link.springer.com/article/10.1023/A:1020535511537)...

> Gaming strategies are inherent in any design that requires traders to manipulate their bids in order to take account of factors that the bid format does not allow them to express directly

## Wholesale Electricity Markets
Wholesale markets include auction designs for allocating rights to resources (i.e., capacity or energy) and transmission.  Typically there is a single network operator responsible for physical operation of the grid infrastructure in a particular geographic area.  System operator coordinates generator schedules, handles load balancing and supply of resources in real time.  Not obvious to what extent the system operator needs to be involved in energy an ancillary markets. For example, should the system operator also be responsible for administering the day-ahead forward/futures and near real-time auctions for energy and capacity?

Case for centralized wholesale markets is strongest when there is strong competition and good optimization (of what?); case for more decentralized, bilateral markets is strongest when scheduling decisions of market participants are important. Well functioning bilateral markets would seem to require some sort of centralized near-real time market to smooth flucuations. To what extend will blockchain technologies, cloud computing, and smart homes impact this dynamic?

### Constraints on energy auctions
Constraints on electricity generators significantly complicate the auction design problem as the constraints create opporuntities for individual generators to engage in strategic bidding. Major contribution of our EDSL is that it will allow us to explore the auction space in order to find designs that minimize the prospect for strategic bidding whilst taking into account the generation constraints.

Historically incorporating demand for energy into the auction process has been challenging. In order for load to participate directly in forward and real-time markets smart metering technology is necessary.  In some cases load must be directly dispatchable (i.e., controllable!) by the system operator.

### Settlement Systems
Settlement systems define the rules concerning the price(s) that willbe paid to suppliers or by buyers.  Two basic types of settlement:

* Single-settlement: in single settlement systems, prices that are originally agreed in forward markets are actually settled at real-time spot prices (which might be different).  Single-settltment creates an incentive for market participants to attempt to manipulate the day-ahead forward market in order to influence the price in the real-time market at which transaction are processed. Large market participants are best placed to take advantage of these types of manipulations. Potential for manipulation and added complexity deters entry.
* Multi-settlement: multi-settlement mitigates gaming.  Multi-settlement system decouples the day-ahead forward market from the real-time market. Ask and bid orders issued in the day-ahead market are binding financial commitments. Real-time market prices only apply to ask and bid orders submitted in the real-time market.  No incentive for participants in the day-ahead forward market to try and manipulate the real-time market price.

Uniform price (i.e., non-discriminatory) versus "pay-as-bid" (i.e., discriminatory) auctions are important distinction.  Winner's curse arguments would favor uniform pricing, influence of inframarginal capacity on market-clearing prices argues for a "pay-as-bid" pricing rule. No obvious which of these two effects dominate.  API should accommodate both types of pricing rules

## Examples of auction designs from the U.S.
Day-ahead forward market: periodic double auction with uniform pricing is used for scheduling and unit commitments for the following day.  Settlement is based on real-time [locational marginal prices](http://www.caiso.com/docs/2004/02/13/200402131607358643.pdf) or zonal prices.

Importance of reverse auctions for use in procurement of [ancillary services](https://en.wikipedia.org/wiki/Ancillary_services_(electric_power)). Important for renewables: technological advances (particularly blockcahin) could facilitate competition in these auctions. Ancillary services are required in order for market to function as such, ISO typically act as "providers of last resort" for these services. Issues to consider: activity rules, transmission congestion limiting ability of supply to resond to higher prices, real-time demand is highly inelastic (for technological reasons). Design of such auctions in the U.S. has converged towards the "smart buyer model."

### Installed Capacity Markets: 
ISO pays generators for reserving capacity to meet reliability requirements for the system.  LSEs must contract with generators to obtain a capacity that exceeds peak load within a certain time frame. Formal and informal secondary markets exist inwhich capacity obligations are traded? What role might blockchain technology play in organizing this market?

Why does installed capacity market exist? Demand in these markets is highly price inelastic in the short-term and currently does not directly participate in wholesale markets.

## Retail Electricity Markets

### References

* [_Power Market Auction Design_, Edison Electric Institute, 2001](http://web.mit.edu/esd.126/www/MktsAuctions/EEI.pdf) 
