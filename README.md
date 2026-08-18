# Nairobi Developer Workshop: Python for Urban Logistics & Ledger Automation

A free, one-day, hands-on Python workshop for 60 early-career developers, database administrators, and computer science students in Nairobi.

The workshop covers two practical areas:

1. **Spatial logistics optimization** — using Python and GeoPandas to work with geographic coordinates, calculate distances, and construct capacity-aware routes.
2. **Financial ledger data isolation** — using Python's `Decimal` and Django ORM to perform exact monetary calculations and keep tenant-owned ledger records separate at the application layer.

## Event Information

| | |
|---|---|
| **Title** | Nairobi Developer Workshop: Python for Urban Logistics & Ledger Automation |
| **Date** | 20 February 2027 |
| **Location** | Nairobi, Kenya |
| **Format** | One-day, in-person workshop |
| **Instruction** | 6 hours: 3-hour morning track and 3-hour afternoon track |
| **Audience** | Early-career developers, database administrators, and computer science students |
| **Capacity** | 60 participants |
| **Participant cost** | Free for accepted participants |
| **Organizer** | Harsam Transport and Logistics \| ShuleLink |

## Registration

Registration is available through Eventbrite:

**[View the workshop and register](https://www.eventbrite.com/e/nairobi-developer-workshop-python-for-urban-logistics-ledger-automation-tickets-1996821232058)**

Participants must bring a laptop with Python installed. Setup instructions will be shared with accepted participants before the workshop. Lunch and refreshments will be provided.

For event or accessibility questions, contact **Dennis Irumba Mwachiro** at **irumba@shulelink.com**.

## Detailed Curriculum

The complete six-hour teaching plan, exercises, code examples, expected results, and participant checkpoints are available in:

**[CURRICULUM.md](CURRICULUM.md)**

### Schedule Summary

All times are East Africa Time (EAT, UTC+3). Breaks and lunch are not included in the six instructional hours.

| Time | Activity |
|---|---|
| 08:30-09:00 | Arrival and optional setup support |
| 09:00-10:30 | Coordinates, GeoDataFrames, coordinate reference systems, and distance calculation |
| 10:30-10:45 | Break |
| 10:45-12:15 | Greedy route construction and capacity-aware routing lab |
| 12:15-13:15 | Lunch |
| 13:15-14:45 | Exact monetary values, tenant-aware Django models, and unsafe-query demonstration |
| 14:45-15:00 | Break |
| 15:00-16:30 | Tenant-scoped ledger service, isolation tests, and reconciliation lab |
| 16:30-17:00 | Review, questions, feedback, and next steps |

## Learning Outcomes

By the end of the workshop, participants should be able to:

- create and transform geographic data with pandas and GeoPandas;
- explain why coordinate reference systems matter when calculating distance;
- build and assess a simple greedy nearest-neighbour route;
- extend a route to respect vehicle capacity;
- use `Decimal` correctly for exact monetary calculations;
- model tenants, accounts, and ledger entries with Django ORM;
- recognize unsafe unscoped ORM queries;
- implement tenant-scoped ledger reads, writes, lookups, and totals;
- test that one tenant's records are excluded from another tenant's application-level results.

## Workshop Format and Support

The workshop combines short explanations, instructor demonstrations, guided coding, pair exercises, and completion checkpoints.

The planned facilitation team consists of one lead instructor and five roaming mentors. Participants will be arranged into six support groups of approximately ten people to maintain the planned 1:10 support ratio during hands-on exercises.

## Participant Prerequisites

Participants should have:

- Python 3.11 or later;
- Git;
- a code editor;
- basic familiarity with Python variables, functions, loops, lists, and dictionaries.

No prior experience with GeoPandas, routing algorithms, Django ORM, or financial systems is required.

## Curriculum Materials

The repository currently provides:

| File | Purpose |
|---|---|
| `README.md` | Event overview and repository navigation |
| `CURRICULUM.md` | Detailed curriculum draft |

Tested starter code, fictional datasets, participant exercises, and instructor solutions are planned for publication before the workshop. The workshop materials will use simulated location and financial data only; they will not contain attendee locations, real mobile identifiers, customer information, or real financial records.

## Code of Conduct and Incident Reporting

This event is organized by **Harsam Transport and Logistics | ShuleLink** and adopts the [Python Software Foundation Code of Conduct](https://policies.python.org/python.org/code-of-conduct/).

All attendees, speakers, volunteers, and organizers are expected to follow the Code of Conduct throughout the event.

To report a concern or incident before, during, or after the workshop, contact:

**Dennis Irumba Mwachiro — Event Organizer**  
**Email:** irumba@shulelink.com

Dennis will be present throughout the workshop. Reports will be handled promptly, discreetly, and respectfully.

## Contact

**Organizer:** Harsam Transport and Logistics | ShuleLink  
**Event contact:** Dennis Irumba Mwachiro  
**Email:** irumba@shulelink.com  
**Website:** https://www.myshulelink.com
