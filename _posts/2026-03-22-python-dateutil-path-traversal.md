---
layout: post
title: "CVE Request: Path Traversal in python-dateutil rrulestr() TZID Parameter"
tags: vulnerability cve path-traversal python security featured
author: xlaesch
pretty-date: 22 March, 2026
excerpt: "python-dateutil <= 2.9.0.post0 allows path traversal via unsanitized TZID values in rrulestr(), enabling arbitrary file reads and file existence disclosure."
---

## Summary

python-dateutil <= 2.9.0.post0 allows path traversal via unsanitized TZID values in `rrulestr()`. Attacker-controlled RFC 5545 recurrence rule strings can trigger arbitrary file reads and file existence disclosure on the host filesystem.

<!-- more -->

## Vulnerable Piece

`dateutil.rrule._rrulestr._parse_date_value()` — extracts TZID from input and passes it directly to `dateutil.tz.gettz()` with no path validation.

## Root Cause

The TZID parameter is extracted via regex (`TZID=(?P<name>[^:]+):`) and forwarded to `gettz()`, which joins it with system timezone paths using `os.path.join()`. Directory traversal sequences like `../` are never stripped or rejected.

**Data flow:**
```
rrulestr(user_input)
  → _parse_date_value() extracts TZID     # rrule.py:1576
  → tz.gettz(unsanitized_tzid)            # rrule.py:1591
  → os.path.join("/usr/share/zoneinfo", unsanitized_tzid)  # tz.py:1634
  → open(resolved_path, 'rb')             # tz.py:464
```

## Impact

**File existence oracle** — three distinguishable outcomes reveal file state:

| Response | Meaning |
|---|---|
| `ValueError("magic not found")` | File exists, readable |
| `PermissionError` | File exists, not readable |
| Silent `None` / no error | File does not exist |

**File read** — the first 4 bytes of any targeted file are read. Files conforming to TZif format are fully parsed, with content accessible via `tzname()`.

## PoC

```python
from dateutil.rrule import rrulestr

# Probes /etc/passwd existence — raises ValueError("magic not found")
rrulestr(
    "DTSTART;TZID=/etc/passwd:20200101T000000\nRRULE:FREQ=YEARLY",
    forceset=True
)
```

## CWE

- CWE-22 (Path Traversal)
- CWE-204 (Observable Response Discrepancy)

## Mitigation

Pass `tzids=<dict or callable>` to `rrulestr()` to prevent the default `gettz()` dispatch.
