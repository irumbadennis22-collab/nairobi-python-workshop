# Nairobi Developer Workshop: Python for Urban Logistics & Ledger Automation

A one-day, hands-on Python workshop for developers in Nairobi, focused on two practical domains:
1. **Spatial logistics** — routing, geocoding, and distance/coverage analysis for delivery and mobility use cases in an African urban context.
2. **Financial ledger data isolation** — safe parsing, validation, and reconciliation of monetary data using Python's `decimal` module (avoiding the float-precision bugs that plague real-world fintech/ledger systems).

This repository is the working curriculum for the event: registration details, the full day's schedule, learning outcomes, starter code, and exercises. It is submitted as supporting material for a Python Software Foundation (PSF) grant application.

---

## Event Overview

| | |
|---|---|
| **Title** | Nairobi Developer Workshop: Python for Urban Logistics & Ledger Automation |
| **Format** | 1-day, in-person workshop |
| **Location** | Nairobi, Kenya — *[VENUE NAME — TO BE CONFIRMED]* |
| **Date** | *[DATE — TO BE CONFIRMED]* |
| **Audience** | Intermediate Python developers (comfortable with functions, basic OOP, and pip-installable packages) |
| **Capacity** | *[e.g., 30 participants — TO BE CONFIRMED]* |
| **Cost** | Free to attendees (funded via PSF grant) |
| **Organizer** | *[YOUR NAME / ORGANIZATION — TO BE CONFIRMED]* |

## How to Register

Registration is handled via Eventbrite.

👉 **[EVENTBRITE LINK — ADD ONCE LIVE]**

Steps to register:
1. Click the registration link above.
2. Fill in your name, contact email, and current Python experience level.
3. You'll receive a confirmation email with the venue address and a pre-workshop setup checklist (Python 3.11+, `git`, and a code editor).
4. Space is limited — a waitlist will open automatically once capacity is reached.

If you have accessibility needs or questions before registering, open a [GitHub Issue](../../issues) in this repository or contact *[ORGANIZER EMAIL — TO BE CONFIRMED]*.

---

## Schedule (1 Day)

All times are EAT (Nairobi, UTC+3).

### Morning Session — Spatial Logistics (09:00–12:30)

| Time | Session | Details |
|---|---|---|
| 09:00–09:20 | Welcome & Setup Check | Environment verification, intros, workshop goals |
| 09:20–10:15 | **Session 1: Geospatial Data Foundations** | Working with `GeoPandas`, `shapely`, and coordinate reference systems (CRS); loading Nairobi ward/road-network sample data |
| 10:15–10:30 | Break | |
| 10:30–11:30 | **Session 2: Routing & Distance Calculations** | Building the `spatial_routing.py` module live — nearest-warehouse assignment, haversine vs. projected distance, basic route optimization for delivery stops |
| 11:30–12:30 | **Session 3: Hands-On Lab** | Participants extend `spatial_routing.py` to solve a mini logistics case study (assigning delivery riders to zones by proximity and load capacity) |

### Lunch Break (12:30–13:30)

### Afternoon Session — Financial Ledger Data Isolation (13:30–17:00)

| Time | Session | Details |
|---|---|---|
| 13:30–14:15 | **Session 4: Why Floats Break Ledgers** | Demonstrating float rounding errors in financial contexts; introducing Python's `decimal.Decimal` |
| 14:15–15:15 | **Session 5: Building `ledger_isolation.py`** | Parsing raw transaction strings/CSV rows into validated `Decimal` amounts; isolating malformed or ambiguous entries instead of silently coercing them |
| 15:15–15:30 | Break | |
| 15:30–16:30 | **Session 6: Reconciliation Lab** | Hands-on exercise: reconcile a mock ledger with intentionally "dirty" data (currency symbols, mixed separators, trailing whitespace, empty rows) |
| 16:30–17:00 | Wrap-Up & Q&A | Recap, resources for continued learning, feedback form |

---

## Learning Outcomes

By the end of this workshop, participants will be able to:

- Load, clean, and visualize geospatial data using `GeoPandas` and `shapely`.
- Calculate distances and basic optimal routing/assignment for delivery-style logistics problems.
- Explain why `float` arithmetic is unsafe for financial calculations and demonstrate the failure with concrete examples.
- Use Python's `decimal` module to safely parse, validate, and isolate malformed monetary data from mixed-quality real-world input.
- Apply defensive parsing patterns (isolate-and-flag rather than silently coerce) that are directly transferable to production fintech/ledger codebases.
- Leave with two working, extensible code templates (this repo) they can adapt for their own projects.

## Curriculum Materials in This Repository

| File | Purpose |
|---|---|
| `README.md` | This file — event info, schedule, outcomes |
| `spatial_routing.py` | Starter template for the morning session: GeoPandas-based distance/routing utilities |
| `ledger_isolation.py` | Starter template for the afternoon session: `Decimal`-based transaction parsing and isolation of bad records |
| `requirements.txt` | Python dependencies needed for both sessions |
| `data/` | *[Sample/mock datasets to be added — small Nairobi ward geometry sample + mock transaction CSV]* |
| `exercises/` | *[Lab exercise instructions to be added]* |

> **Note to reviewers:** the two `.py` files are working, runnable starter code (not pseudocode) that will be live-extended during the workshop, along with accompanying lab exercises added to `exercises/` ahead of the event date.

---

## Prerequisites for Participants

- Laptop with Python 3.11+ installed
- Basic familiarity with Python functions, lists/dicts, and using `pip`
- `git` installed (to clone this repository on the day)
- No prior geospatial or fintech experience required

## Code of Conduct

This event follows the **PSF Code of Conduct**: https://www.python.org/psf/conduct/

All participants, organizers, and volunteers are expected to abide by it. Instances of unacceptable behavior may be reported to Dennis Irumba|irumba@myshulelink.com or via a confidential [GitHub Issue](../../issues) marked `conduct`.

---

## License

Curriculum materials in this repository are released under the [MIT License](LICENSE) unless otherwise noted, so other communities can freely reuse and adapt them.
