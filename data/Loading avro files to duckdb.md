## Introduction

I recently encountered the challenge of importing a substantial dataset, comprising 13 GB across 13 tables stored in Avro format, into DuckDB. Some of these tables had complex, nested structures, adding to the complexity. My initial attempts using the DuckDB CLI and UI ran into several roadblocks, leading me to explore alternative solutions. This post documents my journey and the successful approach I ultimately adopted.

## Initial Attempts: DuckDB UI and Challenges

DuckDB's UI is generally impressive, and my first instinct was to leverage it for this task. To begin, I installed and loaded the Avro extension:

```sql
INSTALL avro FROM community;
LOAD avro;
```

Loading a single Avro file seemed straightforward:
```sql
CREATE OR REPLACE TABLE sample_table
FROM read_avro('path/to/sample.avro');
```

> [!TIP] 
> DuckDB conveniently allows direct table selection without explicitly using `SELECT *`.

However, I quickly encountered two significant problems:

1. **Incorrect Timestamp Interpretation:** DuckDB interpreted the timestamp fields in the Avro files as integers. these can be manually converted to timestamps but a manual effort nonetheless. 
2. **Memory Allocation Errors:** I faced an "Invalid Input Error: Cannot decode file block: Cannot allocate memory for snappy" error. This suggested that DuckDB struggled to decompress the Snappy-compressed Avro files, possibly due to their large size or limitations in DuckDB's memory management. I even tried the below commands as suggested in [https://duckdb.org/2024/07/09/memory-management.html](Duckdb Memory Management) but no luck! 

```sql
SET memory_limit = '16GB';
SET temp_directory = '/tmp/duckdb_swap'; 
SET max_temp_directory_size = '100GB';
```

These issues prevented me from loading all the files using the DuckDB UI.
## A Python-Based Solution Using `fastavro` and `PyArrow`

Given the challenges with DuckDB's direct Avro ingestion, I explored a Python-based approach. I successfully read the Avro files using the `fastavro` library. To bridge the gap between Python dictionaries (the output of `fastavro`) and DuckDB, I leveraged PyArrow.

PyArrow provides an in-memory columnar data format optimized for analytical workloads. DuckDB has native support for PyArrow tables, enabling efficient data transfer. By converting the Avro data to a PyArrow table, I bypassed the limitations of DuckDB's direct Avro ingestion. I chose Parquet as an intermediary format, as it is a columnar storage format that DuckDB handles very efficiently.

The avro files for each table were stored in a separated directory. The below script iterates each folder and reads the individual file. 

```python
import fastavro
import duckdb
from pathlib import Path
import pyarrow as pa

con = duckdb.connect("/tmp/test.db")

root = Path("~/Downloads/files")

for table in root.iterdir():
    table_name = table.name
    print(f"Reading table: {table_name}")

    rows = []

    for file in table.iterdir():
        print(f"\tReading rows from file {file.name}")
        with open(file, "rb") as f:
            reader = fastavro.reader(f)
            rows.extend([record for record in reader])

    print("Creating a PyArrow Table")
    pa_table = pa.Table.from_pylist(rows)

    print("Loading data into the table")
    con.sql(f"""
        create or replace table {table_name} as 
        select * from pa_table 
    """)


con.close()
```


**Explanation:**

1. **`fastavro` Reading:** The script iterates through each Avro file within the table directories, using `fastavro.reader` to extract the records as Python dictionaries.
2. **PyArrow Conversion:** The list of dictionaries is then converted into a PyArrow `Table` using `pa.Table.from_pylist()`.
3. **DuckDB Ingestion:** Finally, a DuckDB SQL query is executed to create or replace a table with the data from the PyArrow table.


## Fix performance issue 

Since the volume of data was large and it did not fit in my ram I had to tweak the script to dump the data as I read each file instead of reading all data. 

```python 
import fastavro
import duckdb
from pathlib import Path
import pyarrow as pa

con = duckdb.connect("/tmp/test.db")

root = Path("~/Downloads/files")

for table in sorted(root.iterdir()):
    table_name = table.name
    print(f"Reading table: {table_name}")

    files = sorted([file for file in table.iterdir()])
    total_files = len(files)

    for i, file in enumerate(files, 1):
        print(f"\t[{i}/{total_files}] Reading rows from file {file.name}")
        with open(file, "rb") as f:
            reader = fastavro.reader(f)
            rows = list(reader) 

        print(f"\tRows read: {len(rows)}")
        if len(rows) == 0:
            continue
        print("\tCreating a PyArrow Table")
        pa_table = pa.Table.from_pylist(rows)

        print("\tLoading data into the table")
        con.sql(f"""
            create table if not exists {table_name} as 
            select * from pa_table 
        """)

con.close()
```

## Conclusion

By leveraging Python, `fastavro`, and PyArrow, I successfully loaded the large Avro dataset into DuckDB, overcoming the challenges encountered with DuckDB's direct Avro ingestion. This approach highlights the flexibility and power of combining different tools to solve data processing challenges. The seamless integration between DuckDB and PyArrow proved crucial in enabling efficient data transfer.