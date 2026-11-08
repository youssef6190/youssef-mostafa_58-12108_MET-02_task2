# Task 2: Contracts, nested data and a star schema

- **Name:** Youssef Mostafa Shehata
- **ID:** 58-12108
- **Major / Lab:** MET · Lab 02

## What I did

I read 283 lines from `bookings.jsonl`. 21 failed the Pydantic contract: 4 had a `student.id` that is not two digits, a dash and four digits, 4 had a `total` different from the sum of qty × price, 3 had a ticket type outside standard/vip/student, 3 had a ticket `qty` below 1, 3 were booked on or after the event's date, 2 had no event and 2 had no tickets. Another 3 lines were re-sent copies, quarantined as `duplicate booking_id`, so `quarantine.jsonl` holds 24 lines and 259 bookings are left (314 ticket entries). The duplicate check runs after validation so that only valid bookings compete for a `booking_id`: run first, it would keep an invalid first copy, throw away a valid re-send as a "duplicate", and count invalid repeats as duplicates instead of under their real reason. On this file the counts would not change, because all three duplicates are identical valid copies (the notebook runs both orders to show this), but with a broken first copy they would. The field `promo_code` drifted in: it appears in bookings made from 2026-10-20 on (sometimes as `null`); documents without it are not rejected but get `promo_code = NULL` in `bookings`. Finally, `total` belongs to the booking, while the fact's grain is one ticket entry; on the fact it would repeat once per ticket line, so `SUM(total)` would give 42,000 EGP instead of the true 31,285 EGP, double-counting the 55 bookings with two ticket entries. Revenue is `SUM(amount)`.

## Repository

| Path | What it is |
|---|---|
| `task2.ipynb` | the whole pipeline; first cell sets `DATA = "bookings.jsonl"` |
| `bookings.jsonl` | the input, unchanged |
| `quarantine.jsonl` | rejected lines: `line`, `booking_id`, `reason`, original `record` |
| `star/` | `dim_event.parquet`, `dim_student.parquet`, `fact_ticket_sales/booked_month=YYYY-MM/` |
| `requirements.txt` | packages and versions |

Run: `pip install -r requirements.txt`, then `jupyter nbconvert --to notebook --execute task2.ipynb` (or Run All).
