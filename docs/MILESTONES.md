# Milestones

Work these in order. You write the code. Ask the assistant to explain the next concept, review a change you made, or look up an error. Skip ahead and the later steps get muddy, because each one assumes the previous checklist is true.

A milestone is done when every box is checked and you can explain the "concept" line without reading it off the screen.

The product rules live in [SPEC.md](SPEC.md). If a checklist and the spec disagree, the spec wins, and then fix the checklist.

## 1. Tooling and Hello World on a phone

**Goal.** Install the tools, create the Expo app in `app/`, and see a screen you wrote on a physical phone (a simulator is an acceptable backup).

**Concept.** Expo Go is a shell app, already on the phone, that loads the JavaScript you are editing. You change a string, save, and the phone updates. TypeScript checks types before that run. A React component is a function that returns what the screen should show. You are not compiling a native app by hand yet.

**Done when**

- [ ] Node.js LTS, Python 3.11+, and Git run from a terminal, and you can print each version.
- [ ] `app/` exists and was created with Expo's TypeScript template. The README's folder picture matches the repo.
- [ ] The phone, on the same Wi-Fi, shows your screen through [Expo Go](https://expo.dev/go). The screen shows a short line of text you typed yourself.
- [ ] You can say what a component is, in one sentence.

**Read**

- [Set up your environment](https://docs.expo.dev/get-started/set-up-your-environment/)
- [Create a project](https://docs.expo.dev/get-started/create-a-project/)
- [Create your first app](https://docs.expo.dev/tutorial/create-your-first-app/) (through the chapter that gets a screen on the phone)
- [TypeScript from scratch](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html)
- [Your first component](https://react.dev/learn/your-first-component)

## 2. Show a map and drop a pin

**Goal.** Replace the hello-world text with a map, and put a pin at a latitude and longitude you choose (your house, a campus, a park).

**Concept.** The map is another component. You pass it data (coordinates) the way you pass arguments to a function. State is the data the screen keeps; when that data changes, React draws the screen again. A pin is just a marker at a latitude and longitude. Start on Android if you want the pin on Google Maps inside Expo Go. On iOS, Expo Go draws Apple Maps, which is the version-one plan in the spec.

**Done when**

- [ ] The phone shows a map you can pan and zoom.
- [ ] One marker sits at a coordinate you typed in code. Changing the numbers and reloading moves the pin.
- [ ] You can explain latitude and longitude well enough to predict which way the pin moves if you increase each number.
- [ ] You re-read the Expo map-view page and confirmed `react-native-maps` is still included in Expo Go for your SDK. If it is not, stop and update [SPEC.md](SPEC.md) before installing a different library.

**Read**

- [react-native-maps (Expo)](https://docs.expo.dev/versions/latest/sdk/map-view/)
- [State: a component's memory](https://react.dev/learn/state-a-components-memory)
- [Maps SDK for Android overview](https://developers.google.com/maps/documentation/android-sdk/overview) (background; Expo Go already carries a map key, so you do not need your own key for this milestone)

## 3. Get a route and a distance between two places

**Goal.** Turn two addresses into a driving distance in miles and a path you can draw on the map from milestone 2.

**Concept.** An HTTP API is a function you call across the network. You send a request, you get JSON back (text that is nested lists and dictionaries). `async` / `await` is how Python and TypeScript wait for that response without freezing the story of the function. The Routes API returns meters; the rest of this project speaks miles, and the conversion happens in one place.

Do the API call in Python, in `backend/`. Python is the familiar ground. Save one real response as a fixture file and have the app draw that fixture, so you can work on the polyline without calling Google on every reload.

**Done when**

- [ ] A Python script reads a Google API key from a gitignored `.env` and calls Routes API **Compute Routes** with a basic driving route (Essentials: leave traffic features off).
- [ ] It prints the distance in miles for two addresses you picked. You can show the meters-to-miles line: divide by `1609.344`.
- [ ] The app draws that path as a polyline and puts a marker at each end, using a saved response so the map still works with the Wi-Fi off to Google.
- [ ] `git status` does not list `.env` or any key.
- [ ] A budget alert exists on the Google billing account.

**Read**

- [Routes API overview](https://developers.google.com/maps/documentation/routes)
- [Get a route](https://developers.google.com/maps/documentation/routes/compute_route_directions)
- [Routes API billing](https://developers.google.com/maps/documentation/routes/usage-and-billing)
- [Geocoding overview](https://developers.google.com/maps/documentation/geocoding/overview) (use this if you geocode the addresses yourself before asking for a route; the Routes API can also take address strings)
- [Python `venv`](https://docs.python.org/3/tutorial/venv.html) and the standard-library [`json`](https://docs.python.org/3/library/json.html) and [`urllib.request`](https://docs.python.org/3/library/urllib.request.html) pages. A small HTTP library is fine too once you can say what the request contains.

## 4. Cost calculator in Python, with tests

**Goal.** A pure function that implements the formula in [SPEC.md](SPEC.md), plus tests that lock the rounding rules. No phone and no network in this milestone.

**Concept.** A pure function takes arguments and returns a result. It does not read the clock, the network, or a global setting. Tests call it with known inputs. You have written functions like this in C; pytest is the runner that calls them and reports failures. Money uses `Decimal`, because binary floats (the same IEEE floats as in C) cannot hold cents exactly.

**Done when**

- [ ] `gallons` and `cost` match the spec formula, implemented with `Decimal` and half-up rounding to the cent.
- [ ] `pytest` passes these cases, which you wrote yourself:
  - 100 miles, 25 mpg, $4.00/gallon → $16.00
  - 0 miles, 25 mpg, $4.00/gallon → $0.00
  - 10 miles, 30 mpg, $3.339/gallon → $1.11
- [ ] MPG of 0, a negative distance, and a negative price each fail with an error your test expects.
- [ ] The function does not call EIA or Google. Prices arrive as an argument. That split is what makes the tests fast and free.

**Read**

- [SPEC.md](SPEC.md) (the formula is the assignment)
- [`decimal`](https://docs.python.org/3/library/decimal.html), including `ROUND_HALF_UP`
- [pytest getting started](https://docs.pytest.org/en/stable/getting-started.html)
- [Floating point arithmetic](https://docs.python.org/3/tutorial/floatingpoint.html) (the "why Decimal" reading)

## 5. Connect the app to the backend and show the estimate

**Goal.** The phone sends a start, a destination, and an MPG to FastAPI. The server computes the route, looks up the EIA price, runs the milestone-4 function, and returns JSON. The phone shows miles, the region and week of the price, gallons, and the dollar estimate, with the route on the map.

**Concept.** The phone is a client. The backend is a second program on your computer. They talk HTTP and JSON. `localhost` on the phone means the phone itself, so a physical device needs your computer's LAN address, and the server must listen on `0.0.0.0`. CORS is the browser rule that, on web, decides which sites may call your API; set it deliberately when you try the web preview, and keep the allowed origins tight.

**Done when**

- [ ] One FastAPI endpoint accepts the three inputs and returns distance, gallons, price per gallon, the EIA region and period, and cost. It uses the milestone-4 function rather than a second copy of the formula.
- [ ] The EIA call uses your free key from `.env` and the origin-state rule in the spec. The screen shows that the price is a weekly average.
- [ ] pytest covers the endpoint with a fake route and a fake price, so the test does not need the network. One manual run on the phone uses the real APIs.
- [ ] The phone and the computer are on the same Wi-Fi, the app shows a real estimate for a trip you know, and the line on the map matches that trip.
- [ ] A wrong MPG (0 or blank) shows an error on the phone instead of a crash or a made-up dollar amount.
- [ ] Keys stay out of git. The app binary contains no Google or EIA key.

**Read**

- [FastAPI first steps](https://fastapi.tiangolo.com/tutorial/first-steps/)
- [FastAPI testing](https://fastapi.tiangolo.com/tutorial/testing/)
- [CORS](https://fastapi.tiangolo.com/tutorial/cors/)
- [EIA API docs](https://www.eia.gov/opendata/documentation.php) and the [open-data browser](https://www.eia.gov/opendata/browser/) (find the weekly regular-gasoline series for a state)
- [Network](https://reactnative.dev/docs/network) (the `fetch` the app will use)

## 6. Version two: long trips, refuel stops, station prices

**Goal.** When a trip needs more fuel than the tank holds, the app suggests where to refuel and prices those stops from a per-station source. Do the planning in writing before the code.

**Concept.** A larger feature splits into data, math, and UI. The math can be tested the same way as milestone 4: given a tank, an MPG, and a list of stations along a route, decide where to stop. The data source is a separate choice, with a price tag, made before you build around it. Caching matters, because a map that asks for station prices on every pan will burn the paid quota.

**Done when**

- [ ] You updated the station-price section of [SPEC.md](SPEC.md) with a source you re-checked that week, including the cost and what you still could not verify.
- [ ] The user can enter a tank size (gallons) or a starting fuel amount. A trip that fits in the tank looks like version one, with no forced stop.
- [ ] A trip that does not fit shows one or more stations on the route, each with a price, and a total fuel cost that uses those prices for the gallons bought there.
- [ ] The stop-picking function has pytest cases, including "fits in the tank" and "needs a stop," using fixture stations so the tests do not call the paid API.
- [ ] A budget alert still covers the new SKU, and the app does not request station prices on every camera move.

**Read**

- The station-price table in [SPEC.md](SPEC.md)
- [Place data fields](https://developers.google.com/maps/documentation/places/web-service/data-fields) (`fuelOptions` and which SKU it triggers)
- [Google Maps Platform price list](https://developers.google.com/maps/billing-and-pricing/pricing)
- [TomTom Fuel Prices introduction](https://docs.tomtom.com/fuel-prices-api/documentation/product-information/introduction) and [market coverage](https://docs.tomtom.com/fuel-prices-api/documentation/product-information/market-coverage)
- [OPIS retail fuel prices](https://www.opis.com/product/pricing/retail-fuel-prices/)
