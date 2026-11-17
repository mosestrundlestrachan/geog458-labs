# Lab 6 — Smart Dashboard

Moses Trundle Strachan — GEOG 458

A dashboard for Washington county population designed with intention, 2000–2020. The
sidebar on the left holds the controls, the summary number and a D3 trend chart; one large
map panel holds the choropleth, the legend and the navigation control. A single
array of county features is the input to all three views at once.

## Data

39 features, one per Washington county. The file carries housing, household and
group-quarters counts for every year as well; this dashboard uses only the
population series and the identifiers.

Statewide the file totals **5,894,143 in 2000** and **7,656,200 in 2020**, a gain
of 29.9 percent.

The map encodes absolute population in 2020, not population density. The data are historical estimates; this
page is not a live monitoring feed.

## How the dashboard is wired

Three state variables hold the reader's input, and one function turns that state
into a view:

- `matchingCounties` — the subset the search filter produced
- `hoveredCounty` — a temporary preview
- `selectedCounty` — a pinned county

`updateDashboard()` resolves them in priority order with a pinned county, then hovered
county, then the matching subset. The resulting array is then sent to the place
name, the summary number, `highlightCounties()` and `updateLinkedChart()`. Each
interaction in the page ends in a call to that one function, so the map, the text
and the chart always agree about what is selected.

### Step 2 — Hover and click

`mousemove` on `county-fill` returns a rendered feature; its `OBJECTID` is used to
find the original county object, which becomes `hoveredCounty`. A click stores
that county as `selectedCounty`, and hovering no longer changes the sidebar while
a pin is set. Clicking empty map space, or anywhere outside the map except the
search controls, clears the pin.

`highlightCounties()` does not remove anything from the map. Input counties are
drawn at 0.9 fill opacity and outlined by two line layers (a 5 px white)
beneath a 1.5 px black line. Every other county drops to 0.12 opacity and a faint border.

### Step 3 — Search filter

Typing filters `counties` with a case-insensitive substring match on
`NAMELSAD10`, which really does rebuild `matchingCounties`, so the results list,
the summary number and the chart all narrow. Results can be hovered for a
preview, clicked to pin, or reached with Tab and Enter; Escape closes the list.
Changing the query clears any existing pin. 

### Step 4 — Linked D3 trend

`updateLinkedChart(features)` builds 21 points from `d3.range(2000, 2021)`, each
one `d3.sum()` of that year's population across the input counties, and redraws
the axes, line and dots. Nothing about this is map-specific: the same function
serves a hovered county, a pinned county, a search subset, or all 39 counties.
Multiple matches give the **sum** of those counties for each year, not their
average, and the y-axis rescales for every input, so two similar-looking slopes
are not necessarily the same absolute growth.

## Independent task 1 — A different indicator

A **Map indicator** control in the sidebar switches the choropleth between two
indicators:

| Indicator | Field | Classes |
|---|---|---|
| Total population, 2020 | `POP2020` | Under 25,000 · 25,000–99,999 · 100,000–499,999 · 500,000–999,999 · 1,000,000 or more |
| Population change, 2000–2020 | `GROWTH` | Decline · 0–9.9% · 10–24.9% · 25–49.9% · 50% or more |

`GROWTH` is computed once at load time as `(POP2020 − POP2000) / POP2000 × 100`
and written onto each feature before the source is added. Switching the control
swaps the `step` fill-color expression, redraws the legend, rewrites the caption
under the place name, and replaces the note below the status line.

The two indicators are deliberately different kinds of measures. Population is a
count that is dominated by King, Pierce and Snohomish. Percentage change is a
rate, so a small county that grew can read as high growth while adding very few people. 
The summary number changes with the indicator: for population it is the sum of the input 
counties, and for change it is the aggregate rate of the input counties because averaging 
percentages across counties of very different sizes could be misleading. A sequential ramp of blue 
colors keeps the change different from the purple population ramp.

The two maps do not agree, which is the point of offering both. Franklin County
ranks 14th of 39 by 2020 population but first by growth; King County ranks first
by population and eleventh by growth. Only one county, Garfield, lost population.

## Independent task 2 — Hover popup

A `mapboxgl.Popup` with no close button follows the pointer on `mousemove` over
`county-fill` and is removed on `mouseleave`. It reports the county name, its
2020 population, its 2000 population and its percentage change since 2000, as a
small label-and-value table, so both indicators are readable at the pointer
whichever one the map is currently showing. The popup is independent of the pin,
so it still reports whatever the pointer is over while a county is pinned, while
the sidebar keeps showing the pinned county.

## Visual design

The page is typeset and inspired by my friend Owen's website: white paper,
almost black ink, one small type size, hairline rules instead of boxes, and no
shadows or rounded corners. Type is Diatype Variable where it is available, with
Space Grotesk as the web fallback. Controls are reduced to an underline rather
than a filled field, and search results are numbered `01.`, `02.` in the same
manner as the inspiring site's indexes.

The restraint is functional as well as stylistic: the interface is monochrome, so
the only saturated color on the screen belongs to the data: the choropleth map, its
legend and the trend line. Figures are set in tabular numerals so the summary
number does not shift width as the selection changes.

## Questions to explore

**Does a high 2020 population imply high percentage growth since 2000?**

No. The two rankings are close to independent. King County is the largest county
in the state at 2,260,800 people in 2020, and it grew 30.2 percent since 2000. So we 
essentially see the statewide rate of 29.9 percent. Franklin County, 14th by size at
96,760, grew 96.1 percent, more than three times as fast, but this is likely because it 
started from4 9,347. One the other hand we see Garfield County is the smallest county at 2,225 and 
the only one that lost population, down 7.2 percent. Four of the five fastest-growing
counties sit outside the five largest. Size tells you how many people a county
added but the the rate just says tells you how much the place changed relative to itself, 
and as we see a county can score high on one and low on the other.

**How does the map help you form a question the line chart can investigate?**

The map answers "where" first, and the shapes it makes are what prompt the
question. Switching to the change indicator puts a dark block over the Columbia
Basin (Franklin and Benton together) which is not where the population map
is meant to point at at all. Is this steady growth across twenty years, or just 
one big burst? Hovering Franklin sends it to the chart, and the trend splits into 
58.4 percent in the first decade and 23.8 percent in the second, so the growth was 
relatively front loaded. King County's chart does the opposite, 11.2 percent then 17.1 percent. 

**What geographical context would be lost if non-matching counties disappeared?**

The remaining counties would float without the shape of the state around them. A
reader could no longer tell whether a matching set is clustered on the Puget
Sound, strung along the Columbia, or scattered; nor could they see which
non-matching counties sit between or beside them. Searching `son` returns
Jefferson and Mason, and the only reason it reads as a pair of neighbours either
side of Hood Canal is that the rest of the Olympic Peninsula is still drawn
around them. Fading rather than removing keeps the state outline, the relative
size of each county and the neighbourhood relationships, all of which are exactly
what the map contributes to the dashboard that the number and the chart cannot.

**Where does the code turn an interaction result into a new visualization input?**

In `updateDashboard()`. Every event handler: map `mousemove`, map `click`, the
document-level click, the search `input` handler and the result buttons only
write to `hoveredCounty`, `selectedCounty` or `matchingCounties` and then calls
it. `updateDashboard()` resolves those three into one array of features and
passes that same array to `highlightCounties()` and `updateLinkedChart()`. That
single hand-off is the linking: the result of an interaction becomes the input of
another view.
