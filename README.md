# Gas Route Estimator

A small phone app that answers a practical question: **about how much will the fuel cost to drive from here to there?**

You type a start, a destination, and the car's miles per gallon. The app draws the driving route on a map and shows a fuel-cost estimate. Version one uses free weekly U.S. regional average gas prices. Version two, later, adds refuel stops with per-station prices on long trips.

This repository starts as documentation. You write the app and the backend, one milestone at a time. An assistant can explain a concept, review your code, and look things up. The assistant does not write the project for you.

## Goals

- Ship a version you can try on your own phone: two places, a route, a dollar estimate.
- Keep the cost math in Python, with tests, so a wrong formula fails in the terminal before it fails on the phone.
- Learn the pieces on purpose: TypeScript and React Native, then HTTP and JSON, then a small web API.
- Leave accounts, trip history, live traffic, and station-by-station prices out of version one. They are listed in [docs/SPEC.md](docs/SPEC.md).

## How we work

You already know how a program is structured from C and C++, and you can get a Python script to do something useful. This project adds a phone UI and a tiny server. Take it in the order in [docs/MILESTONES.md](docs/MILESTONES.md).

When you want help, ask for one of these:

- **Explain** the next concept before you type.
- **Review** the code you wrote, the way a classmate would read a lab.
- **Research** a doc or an error message and point you at the page.

A milestone is finished when its checklist is true, including the part where you can say in a sentence or two what the new concept is. That is the point of going slowly.

## Tech stack

| Choice | Why this one |
| --- | --- |
| [React Native](https://reactnative.dev/) | One UI codebase runs on Android and iOS. |
| [Expo](https://docs.expo.dev/) | Handles the phone tooling. Expo Go runs your first screen without installing Android Studio or Xcode. |
| [TypeScript](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html) | Adds types you can check before the app runs. If you have written C, the idea of "this value has a type" will feel familiar. |
| [react-native-maps](https://docs.expo.dev/versions/latest/sdk/map-view/) | The map component Expo documents as included in Expo Go, so a pin on a map can happen before a custom native build. Android shows Google Maps. iOS shows Apple Maps until a later build opts into the Google Maps SDK there. |
| [Google Maps Platform](https://developers.google.com/maps) | Geocoding and the Routes API turn two addresses into a path and a distance. The phone displays that path. The server, not the phone, holds the API key. |
| [Python 3](https://docs.python.org/3/) | You already use it. The fuel math and the tests live here. |
| [FastAPI](https://fastapi.tiangolo.com/) | A small HTTP API with typed requests. The phone asks it for a route and a cost. |
| [pytest](https://docs.pytest.org/) | Runs the cost-calculator tests. |
| [U.S. EIA Open Data](https://www.eia.gov/opendata/) | Free weekly average gasoline prices for the country, regions, and selected states and cities. Enough for version one. |

Version two will need a per-station price source. Options and published costs are compared in [docs/SPEC.md](docs/SPEC.md). Pick one when you get there, and check the prices again that week.

## Prerequisites

Install these before milestone 1. Exact menus change; follow the linked install pages if a step on your machine disagrees with this list.

- A computer running macOS, Windows, or Linux, with a terminal.
- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- [Node.js LTS](https://nodejs.org/) (the long-term support release). Expo's current get-started guide expects this.
- [Python 3.11 or newer](https://www.python.org/downloads/) and the `venv` module that ships with it.
- A phone with the [Expo Go](https://expo.dev/go) app, on the same Wi-Fi as the computer. A simulator is a fine backup. On a physical iPhone, Expo's tutorial also asks you to sign in to a free Expo account in both the terminal and Expo Go.
- A code editor. Cursor is a good fit if you want the assistant in the same window.
- A Google account for [Google Maps Platform](https://developers.google.com/maps/documentation/routes). Billing has to be enabled even when you stay inside the monthly free caps. Set a [budget alert](https://developers.google.com/maps/billing-and-pricing/manage-costs) before the first API call. Version one should use the Routes API **Essentials** SKU (a basic driving route, no live-traffic features).
- A free [EIA API key](https://www.eia.gov/opendata/register.php), used from the backend in milestone 5.

Keep API keys in a `.env` file. The `.gitignore` in this repo ignores that file. Keys belong on the server.

## Folder layout

The app and the backend are separate projects in one repository. Create the folders when the milestone that needs them says so. They are empty on purpose right now.

```text
gas-route-estimator/
├── README.md
├── .gitignore
├── docs/
│   ├── SPEC.md            # what version one is, and what it is leaving for later
│   └── MILESTONES.md      # the six steps, in order
├── app/                   # Expo app (TypeScript). Added in milestone 1.
└── backend/               # FastAPI and pytest. Added in milestones 3–5.
```

## Where to go next

1. Read [docs/SPEC.md](docs/SPEC.md) once, so the destination is clear.
2. Start [milestone 1](docs/MILESTONES.md#1-tooling-and-hello-world-on-a-phone).

The first evening is mostly installing tools and seeing your own screen on a phone. That is the right first win.
