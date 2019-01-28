### State

```
STATE A ---- Collect state in B/C/D, then do actions like: (Submit Form), (Validate Key/Value) etc.
│   ├─ STATE B
│   ├─ STATE C
│   ├─ STATE D
│   │   ├─ STATE E  ---- Dispatch action to D
```