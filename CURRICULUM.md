# Nairobi Developer Workshop: Python for Urban Logistics & Ledger Automation

## Detailed Curriculum Draft

**Event date:** 20 February 2027  
**Location:** Nairobi, Kenya  
**Format:** One-day, in-person, hands-on workshop  
**Technical instruction:** 6 hours (two 3-hour tracks)  
**Participants:** 60 early-career developers, database administrators, and computer science students  
**Facilitation:** 1 lead instructor and 5 roaming mentors (1 mentor for every 10 participants)  
**Cost to participants:** Free

This document is the curriculum that will be delivered during the workshop. It is written as a detailed teaching outline with participant exercises, expected results, and completion checkpoints. Final starter files and anonymized sample datasets will be published in this repository before the workshop.

---

## Workshop Purpose

Participants will use Python to solve two related data-engineering problems:

1. Build a small urban-routing workflow from anonymized geographic coordinates.
2. Build a tenant-aware financial ledger that uses exact decimal arithmetic and prevents one tenant's records from being included in another tenant's application-level queries and totals.

The workshop uses small, understandable examples rather than presenting a production system. The instructor will explain where production applications require additional controls, including authentication, authorization, database constraints, row-level security, audit logging, encryption, and independent security review.

## What Participants Will Build

By the end of the workshop, each participant or pair will have:

- a Python program that loads simulated Nairobi-area mobile-endpoint coordinates;
- a GeoPandas workflow that converts coordinate data into an appropriate projected coordinate reference system;
- a distance matrix and a greedy nearest-neighbour route;
- a capacity-aware route extension and a comparison of route distances;
- a small Django project containing tenants, accounts, and ledger entries;
- monetary values stored and calculated with `Decimal` and Django `DecimalField`;
- a tenant-scoped ledger service that requires a tenant for every query;
- automated checks demonstrating that one tenant cannot retrieve or total another tenant's ledger entries through the workshop service layer.

## Learning Outcomes

After completing the workshop, participants should be able to:

1. Explain why latitude and longitude must not be treated as ordinary metre-based coordinates.
2. Create a GeoDataFrame and transform WGS84 coordinates into a projected coordinate reference system appropriate for Nairobi.
3. Calculate distances between stops and construct a simple greedy route.
4. Describe the limitations of a greedy nearest-neighbour algorithm and distinguish a workshop heuristic from a production route-optimization system.
5. Explain why binary floating-point values are unsuitable for exact financial calculations.
6. Use `Decimal` correctly and avoid constructing it from a float.
7. Model tenant ownership explicitly in Django ORM relationships.
8. Recognize an unsafe unscoped ORM query.
9. Implement tenant-scoped reads, writes, and totals at the application-service layer.
10. Write tests that detect cross-tenant leakage.

---

## Participant Prerequisites

Participants should be comfortable with:

- Python variables, functions, loops, lists, and dictionaries;
- running a Python file from a terminal;
- installing packages with `pip`;
- basic object-oriented terminology.

No previous GeoPandas, routing, Django, ORM, or financial-technology experience is required.

## Required Software

Participants will receive installation instructions at least one week before the event.

- Python 3.11 or later
- Git
- A code editor, such as Visual Studio Code
- A modern web browser
- The workshop repository

The repository will provide a pinned `requirements.txt` containing the workshop dependencies, including Django, pandas, GeoPandas, Shapely, and pytest.

## Pre-Workshop Setup Check

Participants will be asked to run:

```bash
git clone <WORKSHOP-REPOSITORY-URL>
cd <WORKSHOP-REPOSITORY-DIRECTORY>
python3 -m venv .venv
```

Activate the environment on macOS or Linux:

```bash
source .venv/bin/activate
```

Activate it on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Install and verify the dependencies:

```bash
python -m pip install -r requirements.txt
python -m pytest tests/test_setup.py
```

Expected result:

```text
1 passed
```

Participants who cannot complete the check may contact the organizer before the workshop. A limited setup-support period will also run before formal instruction begins.

---

## Workshop Timetable

Breaks, lunch, and closing activities are not counted in the six instructional hours.

| Time (EAT) | Activity | Instructional time |
|---|---|---:|
| 08:30-09:00 | Arrival and optional setup support | — |
| 09:00-09:20 | Welcome, goals, data ethics, and routing case study | 20 minutes |
| 09:20-10:00 | Module 1: Coordinates, GeoDataFrames, and CRS | 40 minutes |
| 10:00-10:30 | Module 2: Measuring and validating distance | 30 minutes |
| 10:30-10:45 | Break | — |
| 10:45-11:30 | Module 3: Constructing a greedy route | 45 minutes |
| 11:30-12:15 | Module 4: Capacity-aware routing lab | 45 minutes |
| 12:15-13:15 | Lunch | — |
| 13:15-13:45 | Module 5: Exact monetary values with Decimal | 30 minutes |
| 13:45-14:15 | Module 6: Tenant-aware Django ledger models | 30 minutes |
| 14:15-14:45 | Module 7: Finding cross-tenant query failures | 30 minutes |
| 14:45-15:00 | Break | — |
| 15:00-15:45 | Module 8: Tenant-scoped ledger service | 45 minutes |
| 15:45-16:30 | Module 9: Isolation and reconciliation lab | 45 minutes |
| 16:30-17:00 | Review, questions, feedback, and next steps | — |

**Morning technical instruction:** 180 minutes  
**Afternoon technical instruction:** 180 minutes  
**Total technical instruction:** 360 minutes (6 hours)

---

# Track One: Spatial Logistics with Python and GeoPandas

## Scenario and Dataset

Participants will work with a small simulated dataset representing mobile devices reporting pickup or delivery coordinates in Nairobi. The records are invented for teaching and do not identify or track real people.

Each record contains:

- a generated endpoint identifier;
- latitude and longitude;
- a requested stop duration;
- an optional demand value;
- no name, telephone number, home address, or persistent device identifier.

The instructor will explain that real location data can reveal sensitive movements and must be collected only with a lawful purpose, informed user expectations, limited retention, appropriate security, and access controls. Participants will not collect live attendee locations during the workshop.

## 09:00-09:20 — Welcome, Goals, and Data Ethics

### Instructor explanation

- Introduce the two workshop projects and their final outputs.
- Explain the simulated transit scenario.
- Distinguish a coordinate, a stop, a route, and a schedule.
- Explain why a mobile endpoint is a source of coordinates, not a routing algorithm.
- Establish privacy rules for the workshop dataset.

### Participant check

Participants open the repository and run:

```bash
python -m pytest tests/test_setup.py
```

Mentors group participants into six support zones of approximately ten people each and resolve remaining setup problems.

### Completion checkpoint

Every participant can open the starter notebook or Python files and explain why the workshop uses simulated rather than personal location data.

## 09:20-10:00 — Module 1: Coordinates, GeoDataFrames, and CRS

### Concepts

- Latitude and longitude
- WGS84 (`EPSG:4326`)
- Geometry objects
- GeoDataFrames
- Projected coordinates and metres
- Nairobi's UTM zone (`EPSG:32737`) for this local exercise

### Guided exercise

Participants load the sample endpoint data:

```python
import pandas as pd
import geopandas as gpd

stops = pd.read_csv("data/mock_stops.csv")

stops_geo = gpd.GeoDataFrame(
    stops,
    geometry=gpd.points_from_xy(stops["longitude"], stops["latitude"]),
    crs="EPSG:4326",
)
```

The instructor explains why longitude is passed as `x` and latitude as `y`, then participants inspect:

```python
print(stops_geo.crs)
print(stops_geo[["stop_id", "geometry"]].head())
```

Participants convert the data to a metre-based projection:

```python
stops_m = stops_geo.to_crs("EPSG:32737")
```

### Expected result

- The original GeoDataFrame reports `EPSG:4326`.
- The projected GeoDataFrame reports `EPSG:32737`.
- Projected point coordinates are expressed in metres rather than longitude/latitude degrees.

### Common problems mentors will check

- Reversed latitude and longitude
- Missing or non-numeric coordinates
- Assigning the wrong original CRS
- Measuring distance before projection

### Completion checkpoint

Participants can explain why a raw difference of `0.01` degrees is not a reliable distance in metres.

## 10:00-10:30 — Module 2: Measuring and Validating Distance

### Guided exercise

Participants select a depot and calculate the distance to every stop:

```python
depot = stops_m.loc[stops_m["stop_id"] == "DEPOT-01", "geometry"].iloc[0]
stops_m["distance_from_depot_m"] = stops_m.geometry.distance(depot)

print(
    stops_m[["stop_id", "distance_from_depot_m"]]
    .sort_values("distance_from_depot_m")
)
```

The group compares the result with an intentionally incorrect calculation performed in `EPSG:4326`. The instructor explains that software may return a number even when the unit and method are wrong.

Participants then construct a pairwise distance matrix that the routing algorithm will use.

### Expected result

Participants produce an ordered table showing the depot first at approximately zero metres, followed by stops from nearest to farthest.

### Completion checkpoint

Participants identify:

1. the nearest stop to the depot;
2. the farthest stop;
3. the unit of the calculated distance;
4. the CRS used for the calculation.

## 10:45-11:30 — Module 3: Constructing a Greedy Route

### Concept

The nearest-neighbour heuristic repeatedly travels to the closest unvisited stop. It is understandable and fast for a workshop demonstration, but it does not guarantee the globally shortest route.

### Guided implementation

Participants complete the core function:

```python
def nearest_neighbour_route(distance_matrix, start_index):
    unvisited = set(range(len(distance_matrix)))
    unvisited.remove(start_index)
    route = [start_index]

    while unvisited:
        current = route[-1]
        next_index = min(
            unvisited,
            key=lambda candidate: distance_matrix[current][candidate],
        )
        route.append(next_index)
        unvisited.remove(next_index)

    route.append(start_index)
    return route
```

The instructor walks through the function one iteration at a time:

1. Start at the depot.
2. Keep a set of stops that have not been visited.
3. Measure the available next moves.
4. Select the nearest unvisited stop.
5. Repeat until all stops are visited.
6. Return to the depot.

Participants calculate total route distance and compare it with the original CSV order.

### Expected result

The program prints:

- the ordered stop identifiers;
- each route leg and its distance;
- total route distance in kilometres;
- distance saved or added compared with the unoptimized input order.

The exact numeric output will be published with the finalized dataset so participants can check their work.

### Completion checkpoint

Participants can trace the first two routing decisions and explain why a locally nearest choice may produce a globally imperfect route.

## 11:30-12:15 — Module 4: Capacity-Aware Routing Lab

### Pair exercise

Each pair extends the route so that a vehicle cannot accept demand above its capacity.

Required tasks:

1. Read the demand assigned to each stop.
2. Track remaining vehicle capacity.
3. Return to the depot when the next stop would exceed capacity.
4. Begin another route and continue until all stops are served.
5. Print each route, its load, and its distance.

### Stretch task

Compare two starting-stop choices or run the greedy algorithm from each possible first stop. Report which candidate produces the shortest valid set of routes for the sample data.

### Mentor checkpoint

Mentors use a checklist rather than providing the completed answer:

- Does every stop appear exactly once?
- Does every route start and end at the depot?
- Does any vehicle exceed capacity?
- Is distance calculated in metres or kilometres rather than degrees?
- Does the result remain identical when the same input is run twice?

### Track deliverable

A working routing report containing route order, route load, route distance, and total distance.

### Production limitations discussed

The instructor closes the track by noting that real routing systems may also require road-network distance, traffic, time windows, vehicle types, driver hours, inaccessible roads, pickup-before-drop-off rules, and specialized solvers. The workshop heuristic must not be represented as a complete production optimizer.

---

# Track Two: Financial Ledger Data Isolation with Decimal and Django ORM

## Scenario and Safety Boundary

The sample application represents two fictional organizations using the same ledger application. Each tenant has accounts and ledger entries. The goal is to prevent application queries for one tenant from retrieving or aggregating another tenant's records.

All names and transactions are fictional. No real financial or customer information is used.

The workshop demonstrates **application-layer tenant scoping**. It does not claim that an ORM filter alone is a complete production security boundary. The closing discussion introduces defense in depth, including database constraints, least-privilege roles, row-level security where appropriate, audit logs, and security testing.

## 13:15-13:45 — Module 5: Exact Monetary Values with Decimal

### Demonstration

Participants run:

```python
print(0.1 + 0.2)
```

Expected output:

```text
0.30000000000000004
```

They then compare:

```python
from decimal import Decimal

print(Decimal("0.1") + Decimal("0.2"))
print(Decimal(0.1))
```

### Instructor explanation

- Binary floating-point values approximate many decimal fractions.
- `Decimal` should be constructed from strings or validated decimal input, not from a float.
- Currency, precision, rounding rules, and accounting policy are separate concerns.
- Exact arithmetic does not by itself provide ledger integrity or tenant isolation.

### Participant exercise

Participants implement a small amount parser that rejects blank or malformed input and returns a `Decimal` for accepted values. They add two or three unit tests.

### Completion checkpoint

Participants can explain why `Decimal("0.1")` is preferable to `Decimal(0.1)` and why rejected data must not silently become zero.

## 13:45-14:15 — Module 6: Tenant-Aware Django Ledger Models

### Data model

The starter project contains three central models:

```python
from django.db import models


class Tenant(models.Model):
    name = models.CharField(max_length=120)


class Account(models.Model):
    tenant = models.ForeignKey(Tenant, on_delete=models.PROTECT)
    name = models.CharField(max_length=120)


class LedgerEntry(models.Model):
    tenant = models.ForeignKey(Tenant, on_delete=models.PROTECT)
    account = models.ForeignKey(Account, on_delete=models.PROTECT)
    reference = models.CharField(max_length=80)
    amount = models.DecimalField(max_digits=18, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)
```

### Instructor walkthrough

1. Explain a tenant as the organization that owns data.
2. Explain foreign keys and why ownership is stored explicitly.
3. Explain `PROTECT` for this simplified example.
4. Explain `DecimalField` precision and its limitations.
5. Show how accounts and ledger entries relate to a tenant.
6. Identify an integrity gap: the basic model does not by itself prove that `entry.account.tenant_id == entry.tenant_id`.

The instructor shows the prepared migration rather than spending workshop time building the project from nothing.

### Participant commands

```bash
python manage.py migrate
python manage.py load_workshop_data
python manage.py shell
```

### Completion checkpoint

Participants retrieve the two fictional tenants and can identify which fields establish ownership.

## 14:15-14:45 — Module 7: Finding Cross-Tenant Query Failures

### Unsafe demonstration

The instructor deliberately shows an unscoped query:

```python
LedgerEntry.objects.all()
```

Participants observe that it returns entries belonging to both tenants. They then examine an unsafe total:

```python
from django.db.models import Sum

LedgerEntry.objects.aggregate(total=Sum("amount"))
```

The result blends values belonging to different tenants.

### Correctly scoped query

```python
LedgerEntry.objects.filter(tenant=active_tenant)
```

### Participant failure test

Participants first run a prepared failing test that expects Tenant A's ledger page to exclude Tenant B's entries. The initial unsafe implementation fails. This makes the risk visible before the correction is introduced.

### Completion checkpoint

Participants can point to the missing tenant condition in the unsafe query and explain why hiding another tenant's rows only in the user interface would not solve the problem.

## 15:00-15:45 — Module 8: Building a Tenant-Scoped Ledger Service

### Design rule

The workshop service must receive a tenant explicitly. It must not offer an unscoped list or total operation to the request-handling code.

### Guided implementation

```python
from dataclasses import dataclass
from decimal import Decimal

from django.db import transaction
from django.db.models import Sum

from .models import Account, LedgerEntry, Tenant


@dataclass(frozen=True)
class LedgerTotals:
    tenant_id: int
    total: Decimal


def entries_for_tenant(*, tenant: Tenant):
    return LedgerEntry.objects.filter(tenant=tenant).order_by("created_at", "id")


def total_for_tenant(*, tenant: Tenant) -> LedgerTotals:
    value = entries_for_tenant(tenant=tenant).aggregate(total=Sum("amount"))["total"]
    return LedgerTotals(tenant_id=tenant.id, total=value or Decimal("0.00"))


@transaction.atomic
def create_entry(
    *,
    tenant: Tenant,
    account: Account,
    reference: str,
    amount: Decimal,
) -> LedgerEntry:
    if account.tenant_id != tenant.id:
        raise ValueError("Account does not belong to the active tenant")

    return LedgerEntry.objects.create(
        tenant=tenant,
        account=account,
        reference=reference,
        amount=amount,
    )
```

### Instructor explanation

- Why the tenant is a required keyword argument
- Why both reads and writes require scoping
- Why aggregate queries also need scoping
- Why account ownership is checked before insertion
- Why generic errors should avoid exposing another tenant's data
- Why transactions help group related database changes but do not create tenant isolation automatically

### Participant exercise

Participants complete a missing `entry_by_reference()` service function that must accept a tenant and reference. They verify that the same reference may safely exist under two different tenants without returning the wrong record.

### Completion checkpoint

Mentors review the completed function and confirm that the tenant condition is applied in the database query rather than after records have been loaded.

## 15:45-16:30 — Module 9: Isolation and Reconciliation Lab

### Required tests

Participants complete tests covering the following behaviors:

1. Tenant A lists only Tenant A entries.
2. Tenant B lists only Tenant B entries.
3. Tenant A's total excludes Tenant B amounts.
4. Tenant B's total excludes Tenant A amounts.
5. Creating an entry with an account owned by another tenant is rejected.
6. Looking up a reference under the wrong tenant returns no record.
7. Valid decimal totals are exact and repeatable.

Example test shape:

```python
def test_tenant_total_excludes_other_tenant_entries(tenant_a, tenant_b):
    result = total_for_tenant(tenant=tenant_a)

    assert result.tenant_id == tenant_a.id
    assert result.total == Decimal("1250.00")
```

### Reconciliation extension

Participants import a small fictional statement for one tenant, parse its monetary strings with `Decimal`, and compare its expected total with the tenant-scoped database total. Malformed records are isolated for review rather than silently converted.

### Expected result

```text
All tenant-isolation tests passed.
Tenant A total: 1250.00
Tenant B total: 3400.50
Cross-tenant account assignment: rejected
```

The exact values will correspond to the finalized sample fixtures.

### Track deliverable

A working tenant-scoped ledger service with automated isolation tests and a small reconciliation report.

### Production limitations discussed

The instructor explains that production multi-tenant finance systems require more than the workshop service layer. Topics for further study include:

- authentication and authorization;
- tenant context derived from a trusted server-side identity;
- database constraints linking related tenant-owned records;
- PostgreSQL row-level security where appropriate;
- least-privilege database roles;
- immutable audit records;
- balanced-entry accounting rules;
- idempotency and duplicate detection;
- currency-specific precision and rounding policies;
- transaction boundaries and concurrency;
- independent security and financial-domain review.

---

## Assessment and Participant Support

Assessment is practical and formative rather than exam-based.

### During each module

- Participants run a visible checkpoint.
- Mentors use a shared checklist to identify blocked participants.
- Participants who finish early receive a stretch task.
- The instructor pauses before each new module until the support zones report readiness.

### End-of-track checks

The morning route must visit every stop exactly once, respect capacity, return to the depot, and report distance in a valid unit.

The afternoon test suite must demonstrate tenant-scoped reads, totals, lookups, and writes without cross-tenant records.

### Mentor structure

The room will be divided into six groups of approximately ten participants. The lead instructor teaches from the front while five roaming mentors and the instructor provide the planned 1:10 support ratio during exercises.

---

## Final Repository Materials

The completed workshop repository is planned to contain:

| Path | Purpose |
|---|---|
| `README.md` | Workshop overview, setup, and navigation |
| `CURRICULUM.md` | This detailed curriculum |
| `requirements.txt` | Pinned Python dependencies |
| `routing/` | Morning starter and completed examples |
| `ledger/` | Django starter project and tenant-scoped service |
| `data/mock_stops.csv` | Invented Nairobi-area coordinate data |
| `data/mock_ledger.csv` | Fictional financial records |
| `tests/` | Setup, routing, decimal, and tenant-isolation checks |
| `solutions/` | Instructor solutions released after the workshop |

The repository will not contain real attendee locations, real mobile identifiers, customer information, or real financial records.

## Follow-Up Resources

Participants will receive links to:

- the Python documentation for `decimal`;
- GeoPandas documentation;
- Django model and QuerySet documentation;
- introductory material on coordinate reference systems;
- introductory material on vehicle-routing problems;
- the completed workshop examples and solutions;
- a post-event technical recap and feedback form.

## Curriculum Status

This is the detailed curriculum draft for grant review. File names, fixture values, and expected numeric outputs will be finalized and tested before delivery without changing the stated learning objectives, six-hour instructional structure, or core participant deliverables.
