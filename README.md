# Vehicle Radiator Database

This repository contains a database of vehicle radiator compatibility information extracted from the AdRad catalog.

## Files

- `adrad_text.txt` - Source data file containing vehicle and radiator information
- `radiator_database.sql` - SQL database schema and data

## Database Schema

The database consists of three tables:

### 1. `vehicles` Table
Stores vehicle information with the following columns:
- `id` - Primary key (INTEGER)
- `make` - Vehicle manufacturer (VARCHAR(100))
- `model` - Vehicle model name (VARCHAR(100))
- `chassis` - Chassis code (VARCHAR(20))
- `year_from` - Starting year of production (INTEGER)
- `year_to` - Ending year of production (INTEGER)
- `engine` - Engine description (VARCHAR(200))
- `engine_size` - Engine size (VARCHAR(50))
- `trans` - Transmission type (Auto/Man) (VARCHAR(50))

### 2. `radiators` Table
Stores unique radiator part numbers:
- `radiator_code` - Primary key, radiator part number (VARCHAR(50))

### 3. `vehicle_radiators` Table
Links vehicles to compatible radiators:
- `id` - Primary key (INTEGER)
- `vehicle_id` - Foreign key to vehicles table (INTEGER)
- `radiator_code` - Foreign key to radiators table (VARCHAR(50))

## Database Statistics

- Total vehicles: 2,070
- Total unique radiators: 1,433
- Total vehicle-radiator links: 2,070

## Example Data

For a Honda Civic ED 1.5ltr Manual from 1987-1991:
- Make: HONDA
- Model: CIVIC
- Chassis: ED
- Year From: 1987
- Year To: 1991
- Engine Size: 1.5ltr
- Transmission: Man
- Radiator Code: HON037CM7K

## Usage

To import the database into SQLite:
```bash
sqlite3 radiators.db < radiator_database.sql
```

To query the database:
```sql
-- Find all radiators for a specific vehicle
SELECT v.*, r.radiator_code 
FROM vehicles v 
JOIN vehicle_radiators vr ON v.id = vr.vehicle_id 
JOIN radiators r ON vr.radiator_code = r.radiator_code 
WHERE v.make = 'HONDA' 
  AND v.model = 'CIVIC' 
  AND v.chassis = 'ED';

-- Find all vehicles compatible with a specific radiator
SELECT v.make, v.model, v.chassis, v.year_from, v.year_to 
FROM vehicles v 
JOIN vehicle_radiators vr ON v.id = vr.vehicle_id 
WHERE vr.radiator_code = 'HON037CM7K';
```

## Data Source

Data extracted from AdRad automotive radiator catalog (adrad_text.txt).
