---
layout: post
title:  "How does the electrical grid work?"
category: energy
---

My first cut at understanding how our electrical grid works.

# National Electric Grid

The [North American Power Transmission Grid](https://en.wikipedia.org/wiki/North_American_power_transmission_grid) is actually 5 separate grids, each on their own AC synchronization. Some of them are connected by a high-power DC tie. Two of them are "major" grids (Western and Eastern Interconnections, basically the West and East halves of the US and Canada, respectively) and 3 are "minor" grids: (Texas, Alaska, and Quebec). They include all of the USA and most of Canada. Future questions: Why don't we connect these grids by AC and have one big grid? Why don't we collaborate with Mexico (except for Northern part of Baja Peninsula)? Part of Alaska shows up as it's own isolated Interconnect but Hawaii doesn't, why?

The [U.S. Energy Information Administration](https://www.eia.gov/) has excellent up-to-date data on energy usage and generation.

# Seattle City Light

Seattle City Light (SCL) is a fairly small [Balancing Authority](https://en.wikipedia.org/wiki/Balancing_authority) (BA). It's connected electrically via interchange to [Bonneville Power Administration](https://www.bpa.gov/) (BPAT) and [Puget Sound Energy](https://www.pse.com/) (PSE). These and many others are part of the [Western Interconnection](https://en.wikipedia.org/wiki/Western_Interconnection).

SCL has a slightly larger service area than the actual city of Seattle boundaries, including [all of Shoreline, nearly all of Lake Forest Park, etc](https://en.wikipedia.org/wiki/Seattle_City_Light). See the SCL [Electrical Service Engineers District Map](https://www.seattle.gov/documents/Departments/CityLight/ESEMap.pdf)

SCL is physically bordered by two utilities: [Puget Sound Energy](https://www.pse.com/en/Customer-Service/pse-locations-2) to the South and West, and [Snohomish County PUD](https://esr.snopud.com/Content/Service-Area-Map.htm) to the North.

SCL uses California ISO (CAISO) as it's Reliability Coordinator (RC). They are responsible for balancing supply and demand for SCL. They participate in the [Western Energy Imbalance Market](https://www.westerneim.com/Pages/About/default.aspx). A lot of future questions about this! How is generation ramped up and down to match demand?

Here are some 2022 electricity generation numbers for SCL and the greater BAs that they're connected to. Generation numbers are a bit meaningless because they share generation and loads with their surrounding peers but still gives a good idea.

| Balancing Authority or Entity | Net Electricity Generation for 2022 in Megawatt-hours (MWh) |
|:------------------------------:|:-------------------:|
| Seattle City Light | [6.2 million](https://www.eia.gov/electricity/gridmonitor/dashboard/electric_overview/balancing_authority/PSEI) (on 9.7 million of demand, 3.5 million more came from the interchange (BPAT & PSEI)) |
| PSEI - minor interconnect to SCL | [5.3 million](https://www.eia.gov/electricity/gridmonitor/dashboard/electric_overview/balancing_authority/PSEI) |
| BPAT - major interconnect to SCL | [98 million](https://www.eia.gov/electricity/gridmonitor/dashboard/electric_overview/balancing_authority/BPAT) |
| CAISO - Reliability Operator for SCL | [170 million](https://www.eia.gov/electricity/gridmonitor/dashboard/electric_overview/balancing_authority/CISO) |
| USA Electricity Net Generation: Electric Power Sector | [4.1 trillion](https://www.eia.gov/totalenergy/data/browser/?tbl=T07.02B#/?f=M) |

*To get net generation numbers above: Go to the Electric Grid Monitor page for the appropriate balancing authority, modify the electricity overview table to annual from 01/01/2022 to 12/31/2022, download to CSV, and sum the Net Generation column.*

For power line maps I found these pages, though there's a gap in connections between high and low level:
* Higher level: <https://www.arcgis.com/apps/mapviewer/index.html?layers=d4090758322c4d32a4cd002ffaa0aa12>
* Street level: <https://data-seattlecitygis.opendata.arcgis.com/datasets/2fe3b77319bd496d81745428a0536237/explore>

I'm a little surprised that a couple of 100-161 KiloVolt (KV) lines can power all of Seattle!

Future questions: Is there a detailed power line map of Seattle? Is there real time data on electrical loads at each point on power lines?

# Energy Sources

## USA annual electricity generation by energy source

USA electricity annual energy usage from the [Electricity Net Generation: Electric Power Sector](https://www.eia.gov/totalenergy/data/browser/?tbl=T07.02B#/?f=M). The latest monthly figures from the EIA are through May, 2023. All figures in Megawatt-hours.

| Fuel Type   | June 2012 - May 2013 | June 2022 - May 2023 |
|:-----------:|:--------------------:|:--------------------:|
| Coal        | 1.6 trillion (40%)   | 727 million (18%)    |
| Natural Gas | 1.1 trillion (27%)   | 1.6 trillion (41%)   |
| Nuclear     | 769 million  (20%)   | 770 million (19%)    |
| Hydro       | 269 million (7%)     | 243 milion (6%)      |
| Solar       |   6 million (0%)     | 148 million (4%)     |
| Wind        | 155 million (4%)     | 428 million (11%)    |

## Western Interconnect electricity generation by energy source

The [Western Electric Coordinating Council](https://www.wecc.org/Pages/home.aspx) (WECC) has this for [Net Generation for the Western Interconnect in 2013 and 2021](https://www.wecc.org/epubs/StateOfTheInterconnection/Pages/Net-Generation0706-7913.aspx), which was their most recent year. All figures in million Megawatt-hours. It does better than the national average in renewable energy.

| Fuel Type    |  2013     | 2021      |
|:------------:|:---------:|:---------:|
| Coal         | 251 (29%) | 142 (17%) |
| Gas          | 247 (29%) | 283 (33%) |
| Hydro        | 217 (25%) | 208 (24%) |
| Nuclear      |  57 (7%)  |  57 (7%)  |
| Solar        |   7 (1%)  |  57 (7%)  |
| Wind         |  49 (6%)  |  82 (10%) |
| Other        |  32 (4%)  |  29 (3%)  |

Future questions: How many power plants contribute to net generation on the Western Interconnect in a normal day? How many of them are run on fossil-fuels? How many new ones are in the interconnect application process? Can we predict a future date where it is 100% renewable energy generation?

Future tasks: Use EIA to get more recent data. Use the EIA API to create views that answer some of the above questions in real time.

## SCL electricity generation by energy source

The SCL website [had me thinking that all of their electricity comes from renewable resources](https://www.seattle.gov/city-light/energy/power-supply-and-delivery). They do [own 5 Hydroelectric dams](https://www.seattle.gov/city-light/energy/power-supply-and-delivery/hydroelectric-resources). But it seems like an accounting gimmick and we should be instead watching the energy mix for the greater Western Interconnect.

