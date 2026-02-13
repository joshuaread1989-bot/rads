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
- `engine_type` - Engine fuel type: Diesel, Petrol, or Petrol Turbo (VARCHAR(50))
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
- Vehicles with engine type classification: 510 (24.6%)
  - Diesel: 281
  - Petrol: 115
  - Petrol Turbo: 114
- Vehicles with engine size data: 1,117 (54%)

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

## AI-Enhanced Data Extraction

The database is generated using AI-powered parsing that includes:

### Engine Code Recognition
- **BMW codes**: 318i → 1.8L Petrol, 320d → 2.0L Diesel, 330i → 3.0L Petrol
- **Mercedes codes**: C200 → 2.0L Petrol, C220 → 2.2L Diesel
- Automatic size extraction from model codes and descriptions

### Fuel Type Detection
- **Diesel indicators**: TDI, CDI, HDI, DCI, "Turbo Diesel"
- **Turbo indicators**: TFSI, TSI, FSI, "Turbo"
- **Petrol indicators**: Default for non-diesel engines

### Chassis Code Validation
- **BMW**: E30, E36, E46, E90/E91/E92, E87/E88/E82 (1 & 3 Series)
- **Audi**: B5/B6/B7/B8 (A4), C4/C5/C6 (A6), 8P (A3), 8T (A5)
- Intelligent chassis code detection from model descriptions

### Example AI Enhancements
```
Original: "E30 318i Man. '83-87"
Enhanced: Model=E30, Engine Size=1.8L, Engine Type=Petrol (from 318i code)

Original: "A3 8P 2.0ltr TDI Man. '08-"
Enhanced: Chassis=8P, Engine Size=2.0L, Engine Type=Diesel (from TDI)

Original: "A4 B8 1.8ltr TFSI Man. '08-"
Enhanced: Chassis=B8, Engine Size=1.8L, Engine Type=Petrol Turbo (from TFSI)
```

## Data Source

Data extracted from AdRad automotive radiator catalog (adrad_text.txt) with AI-enhanced parsing.
