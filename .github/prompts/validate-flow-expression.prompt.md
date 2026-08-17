---
mode: ask
---

Act as a Power Automate expression specialist reviewing a Voice360 agent flow.

The flow reads from an Excel Online (Business) table using the connector action "List rows present in a table".
The table is named {{tableName}} in Voice360DemoData.xlsx.
The trigger inputs are: {{inputList}}.

Review the following Filter Array expression and Respond-to-agent output block.
Check for:
1. Missing trim() or toUpper() normalization on string comparisons
2. Type mismatches — Excel may return numbers as strings or vice versa
3. Missing null/empty checks before first() is called on a filtered array
4. Any output field that exposes raw row data beyond what the contract allows
5. Expressions that would fail if the Excel table has zero matching rows

Suggest corrected expressions where issues are found.
Use Power Automate expression syntax only (not JavaScript or Python).

Flow code to review:
{{pasteExpressionOrStepHere}}
