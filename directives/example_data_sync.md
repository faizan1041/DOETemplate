# Data Sync Example

## Purpose

Sync data from an external API to a local database or spreadsheet. This is an example directive showing the standard structure.

## Inputs

- `source_url`: API endpoint to fetch data from
- `destination`: Where to write the data (file path or sheet ID)
- `filters`: Optional filters to apply

## Process

### Step 1: Fetch Data

Call `execution/fetch_api_data.py` with the source URL:
```bash
python3 execution/fetch_api_data.py --url "$source_url" --output .tmp/raw_data.json
```

### Step 2: Transform Data

Apply any necessary transformations:
```bash
python3 execution/transform_data.py --input .tmp/raw_data.json --output .tmp/clean_data.json
```

### Step 3: Write to Destination

Write the cleaned data to the destination:
```bash
python3 execution/write_output.py --input .tmp/clean_data.json --dest "$destination"
```

## Output

- `status`: "success" or "error"
- `records_synced`: Number of records processed
- `destination`: Where the data was written

## Error Handling

- If API returns 429 (rate limit): Wait and retry with exponential backoff
- If transformation fails: Log the error, skip malformed records
- If write fails: Retry once, then alert user

## Execution Script

Scripts would live in `execution/` - create them as needed.

## Notes

- This is an example directive to demonstrate the structure
- Replace with your actual workflow
- Add learned edge cases here as you discover them
