# Workshop Curriculum: Python for Urban Logistics & Ledger Automation

This is the full, step-by-step curriculum participants will work through during the workshop. It is designed to be followed live, session by session, with checkpoints so nobody falls behind.

**Format note:** each section below is written the way participants will experience it — a short concept explanation, followed by code they type or run, followed by a checkpoint. This mirrors the structure used by community tutorials like the [Django Girls Tutorial](https://tutorial.djangogirls.org/en/), at a scope appropriate for a single-day workshop.

---

## 0. Pre-Workshop Setup (completed before arrival)

Participants receive setup instructions by email after registering, so workshop time isn't lost to installation problems.

**Checklist sent in advance:**
1. Install Python 3.11 or later.
2. Install `git` and clone this repository: `git clone <repo-url>`
3. Create and activate a virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate   # Windows: venv\Scripts\activate
   ```
4. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
5. Verify setup by running:
   ```bash
   python ledger_isolation.py
   ```
   Expected output: a list of "Parsed transactions" and "Isolated (rejected) records" ending in a one-line summary. If this runs, setup is complete.

A volunteer/organizer is available by email in the week before the event to help anyone stuck on setup, so the morning isn't spent debugging installs.

---

## Morning Session — Spatial Logistics (09:00–12:30)

### 09:00–09:20 | Welcome & Setup Check
- Quick round of introductions (name, one line on what they're hoping to get out of the day).
- Live setup verification: everyone runs `python ledger_isolation.py` and `python spatial_routing.py` to confirm their environment works before we start teaching.
- Workshop goals stated plainly: by lunch, everyone will have built and can explain a nearest-hub routing assignment tool.

### 09:20–10:15 | Session 1: Geospatial Data Foundations

**Concept:** Why can't we just use lat/lon numbers directly for distance math? Introduce the idea of a *coordinate reference system* (CRS) — WGS84 (lat/lon, degrees) vs. a projected CRS (meters), and why mixing them up silently produces wrong answers rather than errors.

**Walkthrough:**
1. Load a handful of Nairobi-area coordinates into a plain `pandas.DataFrame`.
2. Convert to a `geopandas.GeoDataFrame` using `shapely.geometry.Point`:
   ```python
   import geopandas as gpd
   from shapely.geometry import Point

   df["geometry"] = [Point(xy) for xy in zip(df.lon, df.lat)]
   gdf = gpd.GeoDataFrame(df, geometry="geometry", crs="EPSG:4326")
   ```
3. Try computing `.distance()` between two points *without* reprojecting — observe the result is in degrees, not meters, and is meaningless for real-world distance.
4. Reproject to EPSG:32737 (UTM Zone 37S, which covers Nairobi) with `gdf.to_crs(epsg=32737)` and recompute — now distances are in meters and make sense.

**Checkpoint:** Each participant can state, in their own words, why `.distance()` gave a "wrong" answer before reprojecting.

### 10:15–10:30 | Break

### 10:30–11:30 | Session 2: Routing & Distance Calculations

**Concept:** Nearest-neighbor assignment — given a set of delivery stops and a set of hubs, assign each stop to its closest hub.

**Walkthrough (live-coded, building `spatial_routing.py` together):**
1. Define `Hub` and `Stop` as small dataclasses (name, lat, lon).
2. Write `make_geodataframe()` to convert a list of records into a GeoDataFrame.
3. Write `nearest_hub()`: for one stop, compute distance to every hub and return the closest.
4. Write `assign_stops_to_hubs()`: loop the above across all stops, return a results table.
5. Run it on the provided mock Nairobi dataset (CBD, Kilimani, South B, Roysambu stops against Industrial Area / Westlands / Embakasi hubs) and inspect the output table together.

**Checkpoint:** Everyone's script produces the same stop→hub assignment table as the instructor's (used as a live "does everyone's environment/code match" check).

### 11:30–12:30 | Session 3: Hands-On Lab — Capacity-Constrained Routing

**Task participants work on individually or in pairs**, using their own `spatial_routing.py` as a starting point:

1. Each `Hub` already has a `capacity` field (max stops it can serve) — currently unused.
2. Modify `assign_stops_to_hubs()` so that once a hub reaches capacity, further stops fall back to the next-nearest hub with room.
3. **Stretch goal** (for anyone who finishes early): write a `total_distance_by_hub()` function summarizing total assigned distance per hub, to simulate a basic load-balancing report.
4. Instructor and a volunteer TA circulate to help debug in real time.

**Wrap:** 2–3 participants share their approach with the group (5 minutes), highlighting different valid solutions.

---

## Lunch Break (12:30–13:30)

---

## Afternoon Session — Financial Ledger Data Isolation (13:30–17:00)

### 13:30–14:15 | Session 4: Why Floats Break Ledgers

**Concept:** Floating-point arithmetic is binary approximation, and money math needs exact decimal arithmetic.

**Walkthrough (live demo, everyone follows along in a Python shell):**
1. Run `0.1 + 0.2` in Python and observe `0.30000000000000004`, not `0.3`.
2. Discuss a concrete failure mode: summing many small transaction floats causes accumulated rounding error — in a ledger with thousands of rows, totals silently drift from the true amount.
3. Introduce `decimal.Decimal` as the fix:
   ```python
   from decimal import Decimal
   Decimal("0.1") + Decimal("0.2")   # Decimal('0.3') — exact
   ```
4. Key rule discussed explicitly: always construct `Decimal` from a **string**, never from a `float` (`Decimal(0.1)` still inherits the float's imprecision — this is a common trap).

**Checkpoint:** Each participant can explain, without notes, why `Decimal("0.1")` and `Decimal(0.1)` behave differently.

### 14:15–15:15 | Session 5: Building `ledger_isolation.py`

**Concept:** Real transaction data is messy — currency prefixes, thousands separators, stray whitespace, blank rows, malformed entries. The goal is not to "fix" bad data silently, but to *isolate* it for human review.

**Walkthrough (live-coded together):**
1. Write a regex pattern that matches valid amount formats (`1,234.56`, `KES 500`, `-99.99`, etc.) using named groups for currency, sign, and amount.
2. Write `parse_amount()`: attempt the regex match, strip thousands separators, construct a `Decimal`. Return `None` on any failure — explicitly discuss why returning `None` (not `0` or a guess) matters for downstream safety.
3. Write `isolate_ledger()`: loop over raw rows, sorting each into a `parsed` list or a `rejected` list with a human-readable reason (`"empty or whitespace-only"`, `"unparseable format"`).
4. Run against the provided mock "dirty" dataset (`data/mock_transactions.csv`) and review the isolated/rejected rows together as a group — discuss what a real analyst would do with each rejected row.

**Checkpoint:** Everyone's script correctly isolates the same rejected rows as the instructor's reference run.

### 15:15–15:30 | Break

### 15:30–16:30 | Session 6: Hands-On Lab — Reconciliation

**Task:**
1. Given two mock ledgers representing "our records" vs. "the bank's records" (provided as two CSVs in `data/`), write a `reconcile()` function that:
   - Parses both using `isolate_ledger()`.
   - Compares totals.
   - Reports any individual transactions present in one ledger but not the other.
2. **Stretch goal:** write the rejected records out to a CSV for human review, preserving the original raw string exactly as received (never "cleaning" it before flagging).
3. Instructor/TA circulate to help debug.

**Wrap:** group discussion — what would break this approach in a real production system? (e.g., multi-currency conversion, duplicate transaction IDs, timezone-shifted timestamps). This connects the lab back to real-world ledger engineering.

### 16:30–17:00 | Wrap-Up & Q&A
- Recap of both scripts and what participants built.
- Pointers to further learning: GeoPandas docs, `decimal` module docs, and how to extend today's code toward a real project.
- Feedback form distributed (used to improve future sessions and included in grant reporting).
- Instructor shares contact info for follow-up questions.

---

## Materials Referenced in This Curriculum

| File | Used in |
|---|---|
| `spatial_routing.py` | Sessions 1–3 |
| `ledger_isolation.py` | Sessions 4–6 |
| `data/mock_stops_hubs.json` *(to be added)* | Sessions 1–3 |
| `data/mock_transactions.csv` *(to be added)* | Sessions 4–6 |
| `data/mock_ledger_ours.csv`, `data/mock_ledger_bank.csv` *(to be added)* | Session 6 reconciliation lab |

All code shown above is real, runnable starter code already in this repository (`spatial_routing.py` and `ledger_isolation.py`) — the curriculum builds on it live rather than introducing new unseen code on the day.
