## Description
Use this script to find information on potentially flooding rivers in your area.
A small demonstration of my CompEng ability, showing the following skills:

#### Core software engineering
* Python CLI design
* Modular architecture: separation of concerns across four modules: parse (ingest), database (persistence), distance (geospatial logic), and find_a_flood (orchestration/CLI). Each has a single clear responsibility.
* Docstrings and self-documentation: consistent Args/Returns docstrings on every function, plus author attribution headers
#### Data engineering 
* REST API consumption with robust HTTP handling, script queries NOAA's NWPS ArcGIS feature service
* JSON/GeoJSON parsing and normalization: grouping raw gauge features by flood category into a clean nested structure
* Local caching
#### Databases
* SQLite (sqlite3): basic operations - creating tables, inserting, and querying (database.py)
* SQL-injection awareness: parameterized (?) queries for values, plus a scrub_table_name() allowlist sanitizer for dynamic table names. 
#### Geospatial / algorithms
* Haversine great-circle distance: implemented from math primitives (sin/cos/atan2/radians) rather than a library, with unit conversion km→mi (distance.py:23)
* Geocoding with geopy/Nominatim to turn a city+state into coordinates
#### DevOps
* Bash scripting: venv_creator.sh with getopts, a usage function, stderr redirection, and meaningful exit codes (venv_creator.sh)
* PowerShell scripting with setup.ps1 with $ErrorActionPreference, path handling, and the py launcher (setup.ps1)
* Dependency hygiene: pinned requirements.txt, virtualenv isolation (and the README explicitly warns against polluting the global environment)
* Git / version control

## Prerequisites
Prerequisites are installed into a virtual environment, don't mess up your own
environment to run this! Run everything from the python/ directory of this
repository. I wrote everything with that in mind.

### Linux
Create a virtual environment (e.g. `python -m venv venv`) and install
`requirements.txt` into it. River gauge data is downloaded automatically by
`find_a_flood.py` on first run (or with `--refresh_data`) from NOAA's National
Water Prediction Service. You can run `venv_createor.sh` to streamline this.

### Windows
```
.\windows\setup.ps1
.\venv\Scripts\Activate.ps1
```

`setup.ps1` creates `venv\` with the `py` launcher and installs
`requirements.txt`.

### Linux
```
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Usage

Run from the python directory.

```
usage: find_a_flood.py [-h]
                       [--category {low_threshold,no_flooding,action,minor,moderate,major,obs_not_current,not_defined,out_of_service}]
                       [--refresh_data]
                       city state radius

positional arguments:
  city                  The name of a city.
  state                 The two letter abbreviation of the state the city is
                        in.
  radius                The search radius, in miles.

optional arguments:
  -h, --help            show this help message and exit
  --category {low_threshold,no_flooding,action,minor,moderate,major,obs_not_current,not_defined,out_of_service}
                        The category of flooding to search for.
  --refresh_data        Whether or not to rebuild the database of results.

```

When the script runs, it will populate a database with gauge data from NOAA's National Water Prediction Service, then
it navigates that database to find floods within the parameters given by the user.

## Credits
* River Level Information - https://water.noaa.gov/
* Distance on Globe from Haversine Formula - https://andrew.hedges.name/experiments/haversine/
