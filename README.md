# UE_ROVData-Formatter

A small Python utility for normalizing and reformatting ROV telemetry and event data into a consistent CSV format for use in RUMI Unreal Engine as a data table.

This repo is built around a single script that ingests raw CSV/TSV exports from different ROV systems, maps inconsistent headers to a fixed schema, corrects timestamps and depth conventions, and writes standardized output files.

---

## What this does

- Reads `.csv` and `.tsv` files from an `input/` directory
- Normalizes column names using a defined alias map
- Outputs a fixed set of headers expected by Unreal Engine tooling
- Converts timestamps into a consistent ISO-like format
- Forces depth values to be negative (downward)
- Infers vehicle name from the filename when not present in the data
- Writes processed CSVs to an `output/` directory

---

## Output schema

The output CSV always uses the same headers, regardless of the input format:

- Row Name  
- Timestamp  
- Vehicle  
- Longitude  
- Latitude  
- Depth  
- Conductivity  
- PressurePSI  
- SalinityPSU  
- SoundVelocityMS  
- TemperatureC  
- Heading  
- Pitch  
- Roll  
- OxygenUncompensatedConcentrationMicromolar  
- OxygenUncompensatedSaturationPercent  
- SealogEventText  
- SealogEventValue  
- SealogEventChannel  
- SealogEventMilestone  
- SealogEventRating  
- SealogEventVehicle  
- Capture1  
- Capture2  
- Capture1ImagePath  
- Capture2ImagePath  

Each field is populated by matching against known header aliases in the input data.

---

## Header aliasing

Input files often use different names for the same data (for example `lat`, `kalman_lat`, or `dvl_lat`).  
The script resolves this by scanning for known aliases per field and mapping the first match it finds.

If a field is missing:
- `Row Name` defaults to the row index
- `Vehicle` may be inferred from the filename (e.g. `hercules`, `atalanta`)
- Everything else is left blank

---

## Timestamp handling

All timestamps are parsed using `dateutil` and rewritten to a consistent format:

```text
YYYY-MM-DDTHH:MM:SS.mmmZ
```

