---
layout: post
title:  "How many healthcare companies beat the market?"
category: finance
---

# How many publicly-traded healthcare companies beat the market?

Answer as of March 28, 2023. I compared the year over year results the 1241 publicly traded companies in the healthcare sector to the [VTI (Vanguard Total Market Index fund)](https://www.google.com/finance/quote/VTI:NYSEARCA) over the past week, year, 3 years, and 5 years:

| Time frame   | Total weeks that beat the market | Total weeks | Percent |
|:------------:|:--------------------------------:|:-----------:|:-------:|
| Last week    |              324                 |     1241    |   26%   |
| Last year    |            12641                 |    60797    |   21%   |
| Last 3 years |            45767                 |   154150    |   30%   |
| Last 5 years |            71312                 |   228214    |   31%   |

This data certainly has [Survivorship bias](https://en.wikipedia.org/wiki/Survivorship_bias). Many of the companies don't have data that goes back 5 years (in fact the median is just under 6 years, and the youngest company is just a couple of weeks old), in that case I only considered weeks where I had year over year data available.

Code for how I found this here: <https://github.com/matthewdrees/the-dude-compounds>

I got the list of publicly-traded healthcare stocks from the NASDAQ Stock Screener: <https://www.nasdaq.com/market-activity/stocks/screener>

For stock prices I used the free weekly-adjusted company data from Alpha Vantage. For year over year calculations I used the weekly "adjusted close": <https://www.alphavantage.co/documentation/>
