# Lab 5 — Millions of Data, Flow Map

Animated AIS vessel tracks in Puget Sound over a 24 hour period on July 4, 2025.

## Study area and dates

Puget Sound, roughly 47.1 to 48.2 N and 123.5 to 122.2 W, for the single day 2025-07-04. I thought this would be interesting because this area had the puget sound, lake washington, channels, and rivers that are home to the Washington State Ferries, cargo ships, tug boats, and recreational watercraft! The source is MarineCadastre AccessAIS (https://marinecadastre.gov/accessais/); one region and one day held the file to the 418,867 observations shown across 1,420 vessels. AccessAIS timestamps are UTC, and all clock times here are Seattle (pacific) time (PDT).

## Interpretation

When interpreting this map we see that traffic peaks at around 4pm PDT with 1,888 reports and drops to 1,182 by 11pm. We see that cargo ships stay relatively constant at 249 to 462 reports an hour. The densest repeated routes are all mostly near pier 50 in downtown Seattle. The watercraft categories differ heavily in speed: all 5 tankers move, but only 627 of the 1,324 "other" vessels ever exceed 1 knot. These boats are likely moored up. The afternoon passenger peak is consistent with holiday ferry demand and being from Vashon I can support this claim. 

## Feature proposal

I think this map and the presence of other vessels could improve the WSDOT ferry tracker app. The app is already solid for checking where late ferries are. However, it is quite boring and I think adding in other vessels and information on other vessels would be a nice addition. 
