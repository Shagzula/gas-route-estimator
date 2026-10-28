# Spec

Version one is a learning build for a driver in the United States who wants a fuel-cost estimate before a trip. The person using it can type two places and a miles-per-gallon number, then read one dollar figure. They are not asking for turn-by-turn navigation.

You (Dan) are the first user and the person writing it. The screen can look plain.

## Version one

Three features carry this version. Everything else can wait.

1. **Trip inputs.** A start address, a destination address, and the car's MPG, typed by the user.
2. **Route on a map.** The app draws the driving route between those two places and marks both ends. Distance and the path come from the Google Maps Platform Routes API, called by the Python backend. On Android, `react-native-maps` shows that path on Google Maps. On iOS, Expo Go shows the same path on Apple Maps. Google Maps tiles on iOS need a later development build; see open questions.
3. **Fuel-cost estimate.** The backend computes the cost and the app displays it, along with the miles, the price per gallon, and the gallons.

```text
distance_miles = distance_meters / 1609.344
gallons        = distance_miles / mpg
cost_dollars   = gallons * dollars_per_gallon
```

Use `decimal.Decimal` for the money math, and round the displayed cost half-up to the nearest cent. The price is the latest weekly EIA average for regular gasoline in the **origin's state**. If that state series is missing, use the U.S. average and say so on the screen. The screen names the region and the week the price came from, so the estimate is obviously an average, not a quote from a pump.

A zero-mile trip costs $0.00. MPG less than or equal to zero, a negative distance, and a negative price are errors, not guesses.

## Non-goals for version one

Milestone 6 is the first place the station work is allowed. Until then, leave these out:

- Refuel stops, tank size, and per-station prices.
- Accounts, saved trips, and history.
- Extra stops, live traffic, tolls, and turn-by-turn navigation.
- Address autocomplete. Two text fields are enough until the basic loop works.
- Diesel, premium, and electric vehicles.
- Countries outside the United States.
- A store release, app icons, and a custom development build. Expo Go is the version-one runtime.

## Decisions already made

- The phone talks only to our FastAPI backend. Google and EIA keys stay in the backend `.env`.
- Routes API calls stay on the Essentials SKU: a basic driving route, no traffic-aware options. As of the [price list](https://developers.google.com/maps/billing-and-pricing/pricing) updated 2026-10-23 UTC, Compute Routes Essentials includes 10,000 free requests a month, then $5.00 per 1,000. Re-check before you enable billing.
- Map UI library: [`react-native-maps`](https://docs.expo.dev/versions/latest/sdk/map-view/), because Expo lists it as included in Expo Go. Re-read that page at the start of milestone 2 in case a newer SDK moves maps out of Expo Go.
- Price feed for version one: [EIA Open Data](https://www.eia.gov/opendata/), series group `petroleum/pri/gnd` (weekly retail gasoline and diesel). Free API key. It is regional averages, which is what version one asks for.

## Open questions

Settle these before the milestone that needs them. Write the answer down in this file when you do.

1. **Which phone first?** Android shows Google Maps inside Expo Go. iOS in Expo Go shows Apple Maps. Either phone can run milestone 1.
2. **Google tiles on iOS.** Expo's current `expo-maps` library is Android-only for Google Maps and is not in Expo Go. `react-native-maps` can request Google Maps on iOS with `PROVIDER_GOOGLE`, and Expo's page says that needs your own key and a rebuilt binary. Version one accepts Apple Maps on iOS. Revisit only if seeing Google tiles on an iPhone becomes a requirement.
3. **Cross-state trips.** Version one prices the whole trip at the origin state's average. Confirm you want that before milestone 5. A blend of origin and destination is a possible change to the formula.
4. **MPG presets.** Version one is a typed number. A later version could offer a few example cars.
5. **Which station-price source for version two?** Compared below on 2026-10-28. Published prices go stale. Where a vendor does not list a price, the cost is **unverified**.

### Station prices (for milestone 6)

| Source | What it actually is | Published cost | Confidence |
| --- | --- | --- | --- |
| [EIA](https://www.eia.gov/opendata/documentation.php) | Weekly U.S., regional, state, and city averages. No individual stations. Already the version-one source. | Free API key. | Official docs. |
| [Google Places `fuelOptions`](https://developers.google.com/maps/documentation/places/web-service/data-fields) | Per-station fuel prices on Place Details, Nearby Search, or Text Search. Asking for this field bills the Enterprise + Atmosphere SKU. | [Price list](https://developers.google.com/maps/billing-and-pricing/pricing) (2026-10-23): Place Details Enterprise + Atmosphere, 1,000 free events/month, then $25 per 1,000. Nearby Search or Text Search with the same field: 1,000 free, then $40 per 1,000. | Prices are from Google's list. How complete U.S. station coverage is: **unverified** (the docs say prices are updated regularly and do not give a coverage figure). |
| [TomTom Fuel Prices](https://docs.tomtom.com/fuel-prices-api/documentation/product-information/introduction) | Per-station prices. The U.S. is marked as detailed coverage. TomTom says data refreshes about every 10 minutes. | Not sold on the free trial or pay-as-you-go plan. Sales quote only. No public price found. | Cost **unverified**. The coverage table's last edit on the page was 2024-05-16, so reconfirm the U.S. before you depend on it. |
| [OPIS retail fuel](https://www.opis.com/product/pricing/retail-fuel-prices/) | Commercial station-price feed. OPIS describes a large North American station set and a realtime price service. | Demo or quote. No public price found. | Cost **unverified**. |
| [CollectAPI Gas Price](https://collectapi.com/api/gasPrice/gas-prices-api) | State and city averages, from third-party docs of their API. That does not answer station prices. | A RapidAPI listing for this API showed 10 requests/month on the free plan and a Pro plan at $39/month. | Price and whether you should prefer it over EIA: **unverified**. Confirm on CollectAPI's own site. |
| GasBuddy | Crowdsourced station prices in their consumer app. No official public developer API turned up on gasbuddy.com. | No public data-license price found. | A license, if they sell one, is **unverified**. Scraping the site is out of the plan. |

**Working default for version two, to challenge when you arrive:** try Google Places `fuelOptions` first. The project already has a Google billing account, and the free monthly cap is enough to learn if you cache results and avoid calling Places on every map move. If prices are missing where you actually drive, ask TomTom and OPIS for quotes before writing the feature around either one.
