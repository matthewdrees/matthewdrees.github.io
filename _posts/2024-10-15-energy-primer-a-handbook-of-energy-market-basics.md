---
layout: post
title:  "Book notes: Energy Primer: A Handbook of Energy Market Basics"
category: book
---

[Energy Primer: A Handbook of Energy Market Basics](https://www.ferc.gov/media/energy-primer-handbook-energy-market-basics), a book-length document put together by the [Office of Energy Policy and Innovation](https://www.ferc.gov/office-energy-policy-and-innovation-oepi), December 2023.

## Wholesale Natural Gas Markets

I didn't care as much about this section, but skimming it shows where different kinds of natural gas are in the ground (p7), interesting maps of where natural gas is extracted in the US, and a description of natural gas production and delivery (p16).

## Wholesale Electric Markets

"To provide electricity on demand, electric system operations have to be planned and conducted with that goal in mind. In the absence of significant amounts of storage and price responsive demand, operators must plan and operate power plants and the transmission grid so that demand and supply exactly match, every moment of the day, every day of the year, in every location" (p34).

1965 power blacokout in the Northeast affected 30 million people and led to the formation of of the North American Electric Reliability Council (NERC). (p35)

FERC order No 888: In 1996, created Independent System Operators (ISOs) and opened transmission access (p37).

FERC order No 889: In 1996, created Open Access Same-Time Information System (OASIS) (p36). This posts available transmission capcity and reserving transmission capacity.

FERC order No 2000: In 1999, encouraged Regional Transmission Organizations (RTOs), which are like ISOs but manage a bigger area. They use bid-based markets. (p37)

Demand response: most customers at the moment have set price throughout the day.
* Curtailing: reduce load throughout the day
* Shifting: move demands to off-peak periods
* On-site generation: (e.g. hospitals, large schools, data centers). Can be dispatchable (controlled by system operator).
* Retail demaind response programs: time-based and interruptable.

Generation: In 2021, the USA had ~1,218 GW of total genrating capacity (p46). An overview of all types of generation.

Renewable energy policies:
* Federal Production Tax Credits (PTC) availble for renewable generation, with bonuses based on labor & source material requirements.
* Federal Investment Tex Credit (ITC) 30% tax credit.
* Steate renewable portfolio standards (RPS): a percentage of energy must be renewable. (p50) [Washington State is 15%](https://www.commerce.wa.gov/energy-policy/eia/#:~:text=The%20EIA%20establishes%20a%20renewable%20portfolio%20standard%20(RPS)%20with%20renewable).
* State renewable energy standards (RES).

Electric storage: pumped-hydro is 21GW but not doing much after that. Battery capacity is predicted to be 30GW by 2025 (p51).

Distributed energy resources (DER): resources located behind a meter (e.g. storage, generation, demand response) (p51). FERC Order No 2222, in 2020, which encourages energy markegs for distributed resources (p52). E.g. solar net metering, demand response appliances. An "aggregator" would combine several smaller resources to an RTO market.

Wholesale electricity markets and trading: Load Serving Entities (LSEs) get power from their own resources, or bilateral transactions with another party or broker like the Intercontinental Exchange (ICE) (p53). To transfer power bewteen Balancing Authority Areas need to request for interchange (eTag) (p54). Cost-based rates where market-based isn't appropriate, estimated using plant costs, production expenses, etc (p55). FERC says market generation sellers can't have "horizontal market power" (i.e. own most available generation), or "vertical market power" (i.e. have barriers to entry or otehrwise influence cost for competitors) (p55).

Transmission service: network (customer can use entire network from multiple generations to multiple loads) or point to point. Point to point has lower priority, can resell unused capacity (p56).

Grid operations: Generation dispatch in 2 stages: RTO/ISO day-ahead unit commitment, then real-time updates. Dispatch planning takes into account many factors. Automatic generation control (AGC) for real-time generation to match supply.

Ancillary services: keep the electric grid reliable in real-time or very near term. Automatic control signal every few seconds (p57). Spinning, non-spinning, and supplemental reserves. Blackstart generation can start on its own.

Traditional electricty systems: In the Southeast and West. Typically own their own generation, transmission, and distribution (p60). Most in the Southeast are on long term contracts.

Real-time energy markets: balances the difference between day-ahead schedule and real-time. 5 min intervals. Shortage pricing with an operating reserve demand curve (p72). Markets set prices, but there are mitigations for situations where markets can't work. Auctions for capacity commitments (p72). Reliability must-run (RMR) are kept around even if they don't run to help with reliability. Financial Transmission Rights (FTRs) to hedge against tranimssion line congestion (p73). Virtual bids for day ahead and real time pricing.

CAISO: Demand response programs for offering to use electricity, either planned or triggered (p95). Real-time bids are submitted upt o 75 minutes before the start of the operating hour. Flexible Ramping Prooduct (p96).

## Gas-electric Interdependency

(Didn't care as much about this section.)

Natural gas use for electricity has gone up about 49% in the last decade (p101).

Even though coal and nuclear units are planned for retirement, natural gas use is predicted to be flat going forward because renewables will take up the slack (p101).

There needs to be good communication for natural gas availability for electric generation for day-ahead markets (p102).

## U.S. Crude Oil and Petroleum Products Markets

Description of different kinds of oil -- light vs dark, sweet vs sour (sulpher) -- in different geographies (p112).

## Trading and Capital Markets

Contracts define the quantity of product, location, price, and timeframe. Timeframe has trade, delivery, and settlement (payment) dates, and can specify peak/non-peak (p121).

Contract types (p122)
* Physical forward contract: straightforward physical delivery between 2 parties.
* Financial forward contract: can happen without delivery, known as a swap.
* Futures: a standard contrac that is traded on an exchange. Many are "cleared cash" and settled at the end of the contract.
* Options: Just like stock market options, can buy the option to buy or sell a contract. Used as a hedge.

Markets: Exchanges, Over-the-Counter (OTC), and RTO/ISO markets (p125).

RTO/ISO operate similar to markets, but additionally have to keep the grid functioning properly (match supply to demand). "In general, if a generator's offer in the day-ahead market clears, the generator's output was offered at or below the day-ahead locational marginal price, and the generator is expected to deliver power." (p125). Also manage fianancial tranmission rights (FTRs). Spread costs of defaults/performance failures across market participants (p126).

Trading analysis and strategy: (p129)
* Fundamental analysis: the overall supply/demand picture.
* Technical analysis: real time price changes.
* Hedging: offsetting position to minimize big losses
* Speculating.

## Market Manipulation

Arguably the most fun section. I'm glad they put this in!

Anti-Manipulation Rule is very broad, because "the methods and techniques of manipulation are limited only by the ingenuity of man" (p134).

Manipulative trading: (p135)
* High-volume "wash" trading to drum up interest.
* "Marking the close" to manipulate the closing price. Also "banging the open".
* Cross-product manipulation: e.g. trade at a loss on the physical market to affect the price on the financial market. A "trigger" and a "target".
* Information-based manipulation. "Pump and dump". False reporting.
* Gaming: transactions for a collateral benefit or against "market design function".
* Withholding. Physical withholding: scheduling plants offline. Economic withholding: set an offer price unreasonbly high.

Examples: (p137)
1. Barclays Bank, et al. 2013. Traders set prices at a loss in the physical market on the InterContinental Exchange to benefit swap positions the next day. $70 million penalty, $35 million disgorgement.
2. BP America, 2013. Same thing, but with natural gas on the Houstaon Ship Channel.
3. Constellation Energy, 2012. Cross-product manipulation on the NYIOS, trigger was physical trading, target was financial swap positions. $135 million penalty, $110 million disgorgement.
4. ETRACOM et al. 2016. Manipulated low price on border of CAISO to get profits on their Congestion Revenue Rights. Penalties + disgorgement = $1.5 million.
5. GDF Suez Energy, 2017. Discounted their day-ahead offers for combustion turbine units to clear the day-ahead market knowing that they wouldn't likely need to run (nor could operate profitably) and took Lost Opportunity Cost credits (LOCs). $41 million penalty, $40.8 million disgorgement.
6. ISO-NE Day-Ahead Load Response Program (DALRP), 2013. Rumford Paper, Lincoln Paper, Competitive Energy Services, & Richard Silkman inflated load baselines, then offered load reductions to increase payments from DALRP.
7. JP Morgan Ventures Energy Corporation, 2013. Twelve manipulative bidding schemes that distorted the market for CAISO. $285 million penalty, $124 million disgorgement.
8. Maxim power corp, 2015. A plant that could burn oil or natural gas, bid the price on oil but actually used natural gas. $50k penalty to the individual, $4 million penalty, $4 million in disgorgement.
9. PGM Up-to congestion cases, 2010. Wash trades to increase volumes wich allowed the traders to claim some credits. $12 million penalty.
10. Golden spread electric cooperative, 2021. SPP has make-whole payments when market participant's costs don't cover short term resources. Golden Spread self-reported needing this payment when they in fact ran profitably. $550k penalty, $375k disgorgement.
11. Greenhat energy, et al, 2021. Lots of fraud around FTRs. $179 million penalty, $13 million disgorgment. Defendants paid what they could and were barred from trading for 10 years.
