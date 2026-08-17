---
applyTo: "**/*.py"
---

# Python instructions — Voice360

## Scope
Python in this project is used only for **workbook generation** (`create_workbook.py`). No production services, no API calls, no authentication logic.

## Standards
- Python 3.8+ compatible syntax only.
- Use `openpyxl` for Excel manipulation — no `xlrd`, `xlwt`, or `pandas` unless explicitly added to requirements.
- All file paths must use `pathlib.Path` or `os.path.join` — never hardcoded absolute paths.
- Output files go to the `data/` directory relative to the script location.
- Print a confirmation line after each file is created: `Created: <relative-path>`.

## Data rules
- All data is **synthetic and fictional** — Contoso Assurance, fictional customers only.
- Never generate realistic-looking National Insurance numbers, real VRNs, or real phone numbers.
- Phone last-four values must be 4-digit strings stored as text, not integers.
- Date of birth values must be stored as `yyyy-MM-dd` strings, not Python date objects, to match Power Automate filter expressions.

## Excel table naming
Tables must be named exactly: `Customers`, `Claims`, `Callbacks`.
Column headers must match the CSV files exactly — no spaces, no camelCase changes.

## Error handling
- Wrap file creation in a `try/except` and print a clear message on failure.
- Do not suppress `ImportError` for `openpyxl` — let it surface so the user knows to install it.
