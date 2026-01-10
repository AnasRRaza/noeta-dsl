# Noeta DSL - Complete Functions Reference

**Version**: 2.0  
**Last Updated**: December 19, 2025  
**Total Functions**: 167 Implemented Operations  
**Status**: Production Ready ✅

---

## Table of Contents

1. [Data Input/Output Operations](#data-inputoutput-operations) (10 functions)
2. [Data Selection & Projection](#data-selection--projection) (7 functions)
3. [Data Filtering](#data-filtering) (9 functions)
4. [Mathematical Operations](#mathematical-operations) (7 functions)
5. [String Operations](#string-operations) (14 functions)
6. [Date/Time Operations](#datetime-operations) (14 functions)
7. [Type Conversion & Encoding](#type-conversion--encoding) (6 functions)
8. [Normalization & Scaling](#normalization--scaling) (4 functions)
9. [Data Cleaning](#data-cleaning) (13 functions)
10. [Data Reshaping](#data-reshaping) (7 functions)
11. [Data Combining](#data-combining) (6 functions)
12. [Aggregation Operations](#aggregation-operations) (20 functions)
13. [Window Functions](#window-functions) (14 functions)
14. [Index Operations](#index-operations) (5 functions)
15. [Statistical Operations](#statistical-operations) (9 functions)
16. [Visualization Operations](#visualization-operations) (5 functions)
17. [Apply/Map Operations](#applymap-operations) (4 functions)
18. [Cumulative Operations](#cumulative-operations) (4 functions)
19. [Time Series Operations](#time-series-operations) (3 functions)
20. [Data Validation](#data-validation) (3 functions)
21. [Boolean Operations](#boolean-operations) (4 functions)
22. [Binning Operations](#binning-operations) (2 functions)

---

## Data Input/Output Operations

### 1. LOAD CSV
**Purpose**: Load data from a CSV file

**Syntax**:
```noeta
load csv "filepath" as alias
load csv "filepath" with delimiter="," encoding="utf-8" as alias
```

**Parameters**:
- `filepath` (required): Path to CSV file
- `delimiter` (optional): Field separator, default=","
- `encoding` (optional): Character encoding, default="utf-8"
- `header` (optional): Row number for column names, default=0
- `skiprows` (optional): Number of rows to skip
- `nrows` (optional): Maximum rows to read

**Examples**:
```noeta
load csv "data/sales.csv" as sales
load csv "data/data.csv" with delimiter=";" encoding="latin-1" as european_data
load csv "data/large.csv" with nrows=1000 as sample
```

---

### 2. LOAD JSON
**Purpose**: Load data from a JSON file

**Syntax**:
```noeta
load json "filepath" as alias
load json "filepath" with orient="records" as alias
```

**Parameters**:
- `filepath` (required): Path to JSON file
- `orient` (optional): JSON structure format ("records", "columns", "index", "split")
- `lines` (optional): Read JSON Lines format (one object per line)

**Examples**:
```noeta
load json "data/sales.json" as sales
load json "data/data.jsonl" with lines=true as streaming_data
```

---

### 3. LOAD EXCEL
**Purpose**: Load data from an Excel file

**Syntax**:
```noeta
load excel "filepath" as alias
load excel "filepath" with sheet="Sheet1" as alias
```

**Parameters**:
- `filepath` (required): Path to Excel file (.xlsx, .xls)
- `sheet` (optional): Sheet name or index, default=0
- `header` (optional): Row number for column names
- `usecols` (optional): Columns to load (e.g., "A:C")

**Examples**:
```noeta
load excel "data/sales.xlsx" as sales
load excel "data/report.xlsx" with sheet="Q1_Sales" as q1_data
```

---

### 4. LOAD PARQUET
**Purpose**: Load data from a Parquet file

**Syntax**:
```noeta
load parquet "filepath" as alias
load parquet "filepath" with columns=["col1", "col2"] as alias
```

**Parameters**:
- `filepath` (required): Path to Parquet file
- `columns` (optional): Specific columns to load
- `engine` (optional): Reading engine ("pyarrow", "fastparquet")

**Examples**:
```noeta
load parquet "data/sales.parquet" as sales
load parquet "data/large.parquet" with columns=["date", "revenue"] as subset
```

---

### 5. LOAD SQL
**Purpose**: Load data from a SQL database

**Syntax**:
```noeta
load sql "query" connection="connection_string" as alias
```

**Parameters**:
- `query` (required): SQL query or table name
- `connection` (required): Database connection string
- `chunksize` (optional): Load in chunks for large results

**Examples**:
```noeta
load sql "SELECT * FROM sales" connection="sqlite:///data/db.sqlite" as sales
load sql "SELECT * FROM users WHERE active=1" connection="postgresql://user:pass@host/db" as active_users
```

---

### 6. SAVE CSV
**Purpose**: Save DataFrame to CSV file

**Syntax**:
```noeta
save dataframe to "filepath.csv"
save dataframe to "filepath.csv" with index=false
```

**Parameters**:
- `dataframe` (required): DataFrame to save
- `filepath` (required): Output file path
- `index` (optional): Include index, default=true
- `header` (optional): Include header, default=true
- `delimiter` (optional): Field separator, default=","

**Examples**:
```noeta
save sales to "output/sales.csv"
save cleaned_data to "output/clean.csv" with index=false
```

---

### 7. SAVE JSON
**Purpose**: Save DataFrame to JSON file

**Syntax**:
```noeta
save dataframe to "filepath.json" format json
save dataframe to "filepath.json" format json orient="records"
```

**Parameters**:
- `orient` (optional): JSON structure ("records", "columns", "index")

**Examples**:
```noeta
save sales to "output/sales.json" format json
save data to "output/data.jsonl" format json orient="records"
```

---

### 8. SAVE EXCEL
**Purpose**: Save DataFrame to Excel file

**Syntax**:
```noeta
save dataframe to "filepath.xlsx" format excel
save dataframe to "filepath.xlsx" format excel sheet="Sheet1"
```

**Parameters**:
- `sheet` (optional): Sheet name, default="Sheet1"
- `index` (optional): Include index

**Examples**:
```noeta
save sales to "output/sales.xlsx" format excel
save report to "output/Q1.xlsx" format excel sheet="Q1_Report"
```

---

### 9. SAVE PARQUET
**Purpose**: Save DataFrame to Parquet file

**Syntax**:
```noeta
save dataframe to "filepath.parquet" format parquet
```

**Parameters**:
- `compression` (optional): Compression type ("snappy", "gzip", "brotli")

**Examples**:
```noeta
save sales to "output/sales.parquet" format parquet
save large_data to "output/compressed.parquet" format parquet compression="gzip"
```

---

### 10. EXPORT PLOT
**Purpose**: Export visualization to image file

**Syntax**:
```noeta
export_plot filename="output.png"
export_plot filename="output.pdf" width=1200 height=800
```

**Parameters**:
- `filename` (required): Output file path (supports .png, .jpg, .pdf, .svg)
- `width` (optional): Width in pixels
- `height` (optional): Height in pixels
- `dpi` (optional): Resolution

**Examples**:
```noeta
boxplot sales columns {price, quantity}
export_plot filename="output/sales_boxplot.png" width=1200 height=800
```

---

## Data Selection & Projection

### 11. SELECT
**Purpose**: Select specific columns from a DataFrame

**Syntax**:
```noeta
select source columns {col1, col2, col3} as alias
```

**Parameters**:
- `source` (required): Source DataFrame
- `columns` (required): List of column names
- `alias` (required): Name for result

**Examples**:
```noeta
select sales columns {product_id, category, price} as products
select data columns {date, revenue} as time_series
```

---

### 12. SELECT BY TYPE
**Purpose**: Select columns by data type

**Syntax**:
```noeta
select_by_type source include=["number"] as alias
select_by_type source exclude=["object"] as alias
```

**Parameters**:
- `include` (optional): Types to include
- `exclude` (optional): Types to exclude

**Examples**:
```noeta
select_by_type sales include=["number"] as numeric_cols
select_by_type data exclude=["object", "datetime"] as only_numbers
```

---

### 13. HEAD
**Purpose**: Select first N rows

**Syntax**:
```noeta
head source n=10 as alias
```

**Parameters**:
- `n` (optional): Number of rows, default=5

**Examples**:
```noeta
head sales n=10 as first_10
head data as top_5
```

---

### 14. TAIL
**Purpose**: Select last N rows

**Syntax**:
```noeta
tail source n=10 as alias
```

**Parameters**:
- `n` (optional): Number of rows, default=5

**Examples**:
```noeta
tail sales n=10 as last_10
tail data as bottom_5
```

---

### 15. ILOC
**Purpose**: Select by integer position

**Syntax**:
```noeta
iloc source rows=[0, 1, 2] as alias
iloc source rows=[0:10] columns=[0, 2] as alias
```

**Parameters**:
- `rows` (optional): Row positions or slices
- `columns` (optional): Column positions

**Examples**:
```noeta
iloc sales rows=[0:5] as first_five
iloc data rows=[10:20] columns=[0, 2, 4] as subset
```

---

### 16. LOC
**Purpose**: Select by label

**Syntax**:
```noeta
loc source rows=["row1", "row2"] as alias
loc source columns=["col1", "col2"] as alias
```

**Parameters**:
- `rows` (optional): Row labels
- `columns` (optional): Column labels

**Examples**:
```noeta
loc sales columns=["price", "quantity"] as price_qty
loc indexed_data rows=["2024-01-01", "2024-01-02"] as jan_data
```

---

### 17. RENAME
**Purpose**: Rename columns

**Syntax**:
```noeta
rename source columns {old_name: new_name, ...} as alias
```

**Parameters**:
- `columns` (required): Mapping of old to new names

**Examples**:
```noeta
rename sales columns {product_id: id, product_name: name} as renamed
rename data columns {col1: date, col2: value} as labeled
```

---

## Data Filtering

### 18. FILTER
**Purpose**: Filter rows based on condition

**Syntax**:
```noeta
filter source where column operator value as alias
filter source where [column > value] as alias
```

**Operators**: `>`, `<`, `>=`, `<=`, `==`, `!=`

**Examples**:
```noeta
filter sales where price > 100 as expensive
filter sales where [price > 50] as mid_high
filter data where category == "Electronics" as electronics
```

---

### 19. FILTER BETWEEN
**Purpose**: Filter rows where value is between two bounds

**Syntax**:
```noeta
filter_between source column=column_name low=value high=value as alias
```

**Parameters**:
- `column` (required): Column to check
- `low` (required): Lower bound (inclusive)
- `high` (required): Upper bound (inclusive)

**Examples**:
```noeta
filter_between sales column=price low=50 high=100 as mid_range
filter_between data column=age low=18 high=65 as working_age
```

---

### 20. FILTER ISIN
**Purpose**: Filter rows where value is in a list

**Syntax**:
```noeta
filter_isin source column=column_name values=[val1, val2] as alias
```

**Parameters**:
- `column` (required): Column to check
- `values` (required): List of acceptable values

**Examples**:
```noeta
filter_isin sales column=category values=["Electronics", "Books"] as selected_cats
filter_isin data column=status values=["active", "pending"] as active_pending
```

---

### 21. FILTER CONTAINS
**Purpose**: Filter rows where string contains substring

**Syntax**:
```noeta
filter_contains source column=column_name value="substring" as alias
```

**Parameters**:
- `column` (required): String column to search
- `value` (required): Substring to find
- `case` (optional): Case sensitive, default=true

**Examples**:
```noeta
filter_contains sales column=product value="Phone" as phones
filter_contains data column=name value="john" case=false as johns
```

---

### 22. FILTER STARTSWITH
**Purpose**: Filter rows where string starts with prefix

**Syntax**:
```noeta
filter_startswith source column=column_name value="prefix" as alias
```

**Examples**:
```noeta
filter_startswith sales column=product value="iPhone" as iphones
filter_startswith data column=code value="A" as a_codes
```

---

### 23. FILTER ENDSWITH
**Purpose**: Filter rows where string ends with suffix

**Syntax**:
```noeta
filter_endswith source column=column_name value="suffix" as alias
```

**Examples**:
```noeta
filter_endswith sales column=product value="Pro" as pro_models
filter_endswith data column=email value=".com" as com_domains
```

---

### 24. FILTER REGEX
**Purpose**: Filter rows matching regular expression

**Syntax**:
```noeta
filter_regex source column=column_name pattern="regex" as alias
```

**Parameters**:
- `column` (required): Column to search
- `pattern` (required): Regular expression pattern

**Examples**:
```noeta
filter_regex sales column=product pattern="^iPhone.*Pro$" as iphone_pros
filter_regex data column=phone pattern="\\d{3}-\\d{3}-\\d{4}" as valid_phones
```

---

### 25. FILTER NOTNA
**Purpose**: Filter rows where value is not null

**Syntax**:
```noeta
filter_notna source column=column_name as alias
```

**Examples**:
```noeta
filter_notna sales column=price as has_price
filter_notna data column=email as has_email
```

---

### 26. FILTER NA
**Purpose**: Filter rows where value is null

**Syntax**:
```noeta
filter_na source column=column_name as alias
```

**Examples**:
```noeta
filter_na sales column=discount as missing_discount
filter_na data column=optional_field as incomplete
```

---

## Mathematical Operations

### 27. ROUND
**Purpose**: Round numeric values to specified decimals

**Syntax**:
```noeta
round source column=column_name decimals=2 as alias
```

**Parameters**:
- `column` (required): Column to round
- `decimals` (optional): Number of decimal places, default=0

**Examples**:
```noeta
round sales column=price decimals=2 as rounded_price
round data column=value decimals=0 as integers
```

---

### 28. ABS
**Purpose**: Calculate absolute value

**Syntax**:
```noeta
abs source column=column_name as alias
```

**Examples**:
```noeta
abs sales column=profit as abs_profit
abs data column=temperature_change as abs_change
```

---

### 29. SQRT
**Purpose**: Calculate square root

**Syntax**:
```noeta
sqrt source column=column_name as alias
```

**Examples**:
```noeta
sqrt sales column=area as side_length
sqrt data column=variance as std_dev
```

---

### 30. POWER
**Purpose**: Raise to power

**Syntax**:
```noeta
power source column=column_name exponent=2 as alias
```

**Parameters**:
- `exponent` (required): Power to raise to

**Examples**:
```noeta
power sales column=radius exponent=2 as area
power data column=value exponent=3 as cubed
```

---

### 31. LOG
**Purpose**: Calculate logarithm

**Syntax**:
```noeta
log source column=column_name base=e as alias
```

**Parameters**:
- `base` (optional): Logarithm base ("e", "10", or number), default="e"

**Examples**:
```noeta
log sales column=value base=e as natural_log
log data column=value base=10 as log10
log sales column=value base=2 as log2
```

---

### 32. CEIL
**Purpose**: Round up to nearest integer

**Syntax**:
```noeta
ceil source column=column_name as alias
```

**Examples**:
```noeta
ceil sales column=price as price_ceil
ceil data column=value as rounded_up
```

---

### 33. FLOOR
**Purpose**: Round down to nearest integer

**Syntax**:
```noeta
floor source column=column_name as alias
```

**Examples**:
```noeta
floor sales column=price as price_floor
floor data column=value as rounded_down
```

---

## String Operations

### 34. UPPER
**Purpose**: Convert string to uppercase

**Syntax**:
```noeta
upper source column=column_name as alias
```

**Examples**:
```noeta
upper sales column=product as product_upper
upper data column=name as name_upper
```

---

### 35. LOWER
**Purpose**: Convert string to lowercase

**Syntax**:
```noeta
lower source column=column_name as alias
```

**Examples**:
```noeta
lower sales column=product as product_lower
lower data column=email as email_lower
```

---

### 36. TITLE
**Purpose**: Convert to title case (first letter of each word capitalized)

**Syntax**:
```noeta
title source column=column_name as alias
```

**Examples**:
```noeta
title sales column=product as product_title
title data column=name as name_title
```

---

### 37. CAPITALIZE
**Purpose**: Capitalize first letter

**Syntax**:
```noeta
capitalize source column=column_name as alias
```

**Examples**:
```noeta
capitalize sales column=category as category_cap
capitalize data column=text as capitalized
```

---

### 38. STRIP
**Purpose**: Remove leading and trailing whitespace

**Syntax**:
```noeta
strip source column=column_name as alias
```

**Examples**:
```noeta
strip sales column=product as product_clean
strip data column=text as trimmed
```

---

### 39. LSTRIP
**Purpose**: Remove leading whitespace

**Syntax**:
```noeta
lstrip source column=column_name as alias
```

**Examples**:
```noeta
lstrip sales column=product as left_trimmed
lstrip data column=text as no_leading
```

---

### 40. RSTRIP
**Purpose**: Remove trailing whitespace

**Syntax**:
```noeta
rstrip source column=column_name as alias
```

**Examples**:
```noeta
rstrip sales column=product as right_trimmed
rstrip data column=text as no_trailing
```

---

### 41. REPLACE STRING
**Purpose**: Replace substring with another string

**Syntax**:
```noeta
replace_string source column=column_name old="old" new="new" as alias
```

**Parameters**:
- `old` (required): String to replace
- `new` (required): Replacement string

**Examples**:
```noeta
replace_string sales column=product old="iPhone" new="Apple iPhone" as updated
replace_string data column=text old=" " new="_" as underscored
```

---

### 42. SPLIT STRING
**Purpose**: Split string by delimiter

**Syntax**:
```noeta
split_string source column=column_name delimiter="," as alias
```

**Parameters**:
- `delimiter` (required): String to split on
- `n` (optional): Maximum number of splits
- `expand` (optional): Create separate columns, default=false

**Examples**:
```noeta
split_string sales column=tags delimiter="," as tag_list
split_string data column=full_name delimiter=" " expand=true as name_parts
```

---

### 43. CONCAT STRINGS
**Purpose**: Concatenate multiple columns into one string

**Syntax**:
```noeta
concat_strings source columns=[col1, col2] separator=" " as alias
```

**Parameters**:
- `columns` (required): Columns to concatenate
- `separator` (optional): String between values, default=""

**Examples**:
```noeta
concat_strings sales columns=[first_name, last_name] separator=" " as full_name
concat_strings data columns=[city, state, zip] separator=", " as address
```

---

### 44. SUBSTRING
**Purpose**: Extract substring by position

**Syntax**:
```noeta
substring source column=column_name start=0 length=5 as alias
```

**Parameters**:
- `start` (required): Starting position (0-indexed)
- `length` (optional): Number of characters

**Examples**:
```noeta
substring sales column=product start=0 length=10 as product_short
substring data column=code start=0 length=3 as prefix
```

---

### 45. LENGTH
**Purpose**: Get string length

**Syntax**:
```noeta
length source column=column_name as alias
```

**Examples**:
```noeta
length sales column=product as product_length
length data column=description as desc_len
```

---

### 46. EXTRACT REGEX
**Purpose**: Extract substring matching regex pattern

**Syntax**:
```noeta
extract_regex source column=column_name pattern="regex" as alias
```

**Parameters**:
- `pattern` (required): Regular expression with capture group
- `expand` (optional): Create separate columns for multiple groups

**Examples**:
```noeta
extract_regex sales column=product pattern="(\\d+)" as product_number
extract_regex data column=email pattern="([a-z]+)@" as username
```

---

### 47. FIND
**Purpose**: Find position of substring

**Syntax**:
```noeta
find source column=column_name value="substring" as alias
```

**Parameters**:
- `value` (required): Substring to find
- Returns -1 if not found

**Examples**:
```noeta
find sales column=product value="Pro" as pro_position
find data column=text value="error" as error_pos
```

---

## Date/Time Operations

### 48. PARSE DATETIME
**Purpose**: Convert string to datetime

**Syntax**:
```noeta
parse_datetime source column=column_name as alias
parse_datetime source column=column_name format="%Y-%m-%d" as alias
```

**Parameters**:
- `format` (optional): Date format string (strftime format)

**Examples**:
```noeta
parse_datetime sales column=order_date as dated
parse_datetime data column=timestamp format="%Y-%m-%d %H:%M:%S" as parsed
```

---

### 49. EXTRACT YEAR
**Purpose**: Extract year from datetime

**Syntax**:
```noeta
extract_year source column=column_name as alias
```

**Examples**:
```noeta
extract_year sales column=order_date as year
extract_year data column=timestamp as year_col
```

---

### 50. EXTRACT MONTH
**Purpose**: Extract month from datetime

**Syntax**:
```noeta
extract_month source column=column_name as alias
```

**Examples**:
```noeta
extract_month sales column=order_date as month
extract_month data column=timestamp as month_col
```

---

### 51. EXTRACT DAY
**Purpose**: Extract day from datetime

**Syntax**:
```noeta
extract_day source column=column_name as alias
```

**Examples**:
```noeta
extract_day sales column=order_date as day
extract_day data column=timestamp as day_col
```

---

### 52. EXTRACT HOUR
**Purpose**: Extract hour from datetime

**Syntax**:
```noeta
extract_hour source column=column_name as alias
```

**Examples**:
```noeta
extract_hour sales column=timestamp as hour
extract_hour data column=datetime as hour_col
```

---

### 53. EXTRACT MINUTE
**Purpose**: Extract minute from datetime

**Syntax**:
```noeta
extract_minute source column=column_name as alias
```

**Examples**:
```noeta
extract_minute sales column=timestamp as minute
extract_minute data column=datetime as minute_col
```

---

### 54. EXTRACT SECOND
**Purpose**: Extract second from datetime

**Syntax**:
```noeta
extract_second source column=column_name as alias
```

**Examples**:
```noeta
extract_second sales column=timestamp as second
extract_second data column=datetime as second_col
```

---

### 55. EXTRACT DAYOFWEEK
**Purpose**: Extract day of week (0=Monday, 6=Sunday)

**Syntax**:
```noeta
extract_dayofweek source column=column_name as alias
```

**Examples**:
```noeta
extract_dayofweek sales column=order_date as weekday
extract_dayofweek data column=date as day_of_week
```

---

### 56. EXTRACT DAYOFYEAR
**Purpose**: Extract day of year (1-365/366)

**Syntax**:
```noeta
extract_dayofyear source column=column_name as alias
```

**Examples**:
```noeta
extract_dayofyear sales column=order_date as day_num
extract_dayofyear data column=date as julian_day
```

---

### 57. EXTRACT WEEKOFYEAR
**Purpose**: Extract week of year (1-52/53)

**Syntax**:
```noeta
extract_weekofyear source column=column_name as alias
```

**Examples**:
```noeta
extract_weekofyear sales column=order_date as week_num
extract_weekofyear data column=date as week
```

---

### 58. EXTRACT QUARTER
**Purpose**: Extract quarter (1-4)

**Syntax**:
```noeta
extract_quarter source column=column_name as alias
```

**Examples**:
```noeta
extract_quarter sales column=order_date as quarter
extract_quarter data column=date as fiscal_quarter
```

---

### 59. DATE DIFF
**Purpose**: Calculate difference between two dates

**Syntax**:
```noeta
date_diff source column1=date1 column2=date2 unit="days" as alias
```

**Parameters**:
- `unit` (optional): Time unit ("days", "hours", "minutes", "seconds"), default="days"

**Examples**:
```noeta
date_diff sales column1=ship_date column2=order_date unit="days" as delivery_time
date_diff data column1=end_date column2=start_date unit="hours" as duration_hours
```

---

### 60. DATE ADD
**Purpose**: Add time period to date

**Syntax**:
```noeta
date_add source column=column_name amount=7 unit="days" as alias
```

**Parameters**:
- `amount` (required): Amount to add
- `unit` (required): Time unit ("days", "hours", "months", "years")

**Examples**:
```noeta
date_add sales column=order_date amount=7 unit="days" as expected_delivery
date_add data column=start_date amount=1 unit="months" as next_month
```

---

### 61. FORMAT DATETIME
**Purpose**: Format datetime as string

**Syntax**:
```noeta
format_datetime source column=column_name format="%Y-%m-%d" as alias
```

**Parameters**:
- `format` (required): Output format (strftime format)

**Examples**:
```noeta
format_datetime sales column=order_date format="%Y-%m-%d" as date_str
format_datetime data column=timestamp format="%B %d, %Y" as readable_date
```

---

## Type Conversion & Encoding

### 62. ASTYPE
**Purpose**: Convert column to specific data type

**Syntax**:
```noeta
astype source column=column_name dtype="type" as alias
```

**Parameters**:
- `dtype` (required): Target type ("int", "float", "str", "datetime", "bool")

**Examples**:
```noeta
astype sales column=price dtype="float" as price_float
astype data column=id dtype="str" as id_str
```

---

### 63. TO NUMERIC
**Purpose**: Convert to numeric, handling errors

**Syntax**:
```noeta
to_numeric source column=column_name errors="coerce" as alias
```

**Parameters**:
- `errors` (optional): Error handling ("raise", "coerce", "ignore"), default="raise"

**Examples**:
```noeta
to_numeric sales column=quantity errors="coerce" as qty_numeric
to_numeric data column=value errors="ignore" as numeric_value
```

---

### 64. ONE HOT ENCODE
**Purpose**: Create binary columns for each category

**Syntax**:
```noeta
one_hot_encode source column=column_name as alias
```

**Parameters**:
- `prefix` (optional): Prefix for new columns
- `drop_first` (optional): Drop first category to avoid multicollinearity

**Examples**:
```noeta
one_hot_encode sales column=category as encoded_sales
one_hot_encode data column=color prefix="color" as color_encoded
```

---

### 65. LABEL ENCODE
**Purpose**: Convert categories to integer labels

**Syntax**:
```noeta
label_encode source column=column_name as alias
```

**Examples**:
```noeta
label_encode sales column=category as category_encoded
label_encode data column=status as status_numeric
```

---

### 66. ORDINAL ENCODE
**Purpose**: Encode categories with specified order

**Syntax**:
```noeta
ordinal_encode source column=column_name order=["low", "medium", "high"] as alias
```

**Parameters**:
- `order` (required): Ordered list of categories

**Examples**:
```noeta
ordinal_encode sales column=priority order=["low", "medium", "high"] as priority_encoded
ordinal_encode data column=size order=["S", "M", "L", "XL"] as size_numeric
```

---

### 67. TARGET ENCODE
**Purpose**: Encode categories based on target variable

**Syntax**:
```noeta
target_encode source column=column_name target=target_column as alias
```

**Parameters**:
- `target` (required): Target variable for encoding

**Examples**:
```noeta
target_encode sales column=category target=revenue as category_target_encoded
target_encode data column=store target=sales as store_performance
```

---

## Normalization & Scaling

### 68. STANDARD SCALE
**Purpose**: Standardize features (mean=0, std=1)

**Syntax**:
```noeta
standard_scale source columns=[col1, col2] as alias
```

**Parameters**:
- `columns` (required): Columns to scale

**Examples**:
```noeta
standard_scale sales columns=[price, quantity] as scaled
standard_scale data columns=[feature1, feature2, feature3] as standardized
```

---

### 69. MINMAX SCALE
**Purpose**: Scale features to range [0, 1]

**Syntax**:
```noeta
minmax_scale source columns=[col1, col2] as alias
```

**Parameters**:
- `feature_range` (optional): Target range, default=[0, 1]

**Examples**:
```noeta
minmax_scale sales columns=[price, quantity] as normalized
minmax_scale data columns=[age, income] feature_range=[0, 1] as scaled
```

---

### 70. ROBUST SCALE
**Purpose**: Scale using median and IQR (robust to outliers)

**Syntax**:
```noeta
robust_scale source columns=[col1, col2] as alias
```

**Examples**:
```noeta
robust_scale sales columns=[price, quantity] as robust_scaled
robust_scale data columns=[value1, value2] as robust
```

---

### 71. MAXABS SCALE
**Purpose**: Scale by maximum absolute value

**Syntax**:
```noeta
maxabs_scale source columns=[col1, col2] as alias
```

**Examples**:
```noeta
maxabs_scale sales columns=[price, quantity] as maxabs_scaled
maxabs_scale data columns=[feature1, feature2] as scaled
```

---

## Data Cleaning

### 72. DROPNA
**Purpose**: Remove rows with missing values

**Syntax**:
```noeta
dropna source as alias
dropna source columns=[col1, col2] as alias
```

**Parameters**:
- `columns` (optional): Specific columns to check
- `how` (optional): "any" or "all", default="any"

**Examples**:
```noeta
dropna sales as clean_sales
dropna data columns=[price, quantity] as no_nulls
dropna sales how="all" as partially_complete
```

---

### 73. FILLNA
**Purpose**: Fill missing values with specified value

**Syntax**:
```noeta
fillna source value=0 as alias
fillna source columns=[col1] value="Unknown" as alias
```

**Parameters**:
- `value` (required): Fill value
- `columns` (optional): Specific columns to fill

**Examples**:
```noeta
fillna sales value=0 columns=[discount] as filled
fillna data value="N/A" as no_nulls
```

---

### 74. FILL FORWARD
**Purpose**: Forward fill missing values

**Syntax**:
```noeta
fill_forward source as alias
fill_forward source column=column_name as alias
```

**Examples**:
```noeta
fill_forward sales as ffilled
fill_forward data column=status as forward_filled
```

---

### 75. FILL BACKWARD
**Purpose**: Backward fill missing values

**Syntax**:
```noeta
fill_backward source as alias
fill_backward source column=column_name as alias
```

**Examples**:
```noeta
fill_backward sales as bfilled
fill_backward data column=status as backward_filled
```

---

### 76. FILL MEAN
**Purpose**: Fill missing values with column mean

**Syntax**:
```noeta
fill_mean source column=column_name as alias
```

**Examples**:
```noeta
fill_mean sales column=price as mean_filled
fill_mean data column=age as avg_filled
```

---

### 77. FILL MEDIAN
**Purpose**: Fill missing values with column median

**Syntax**:
```noeta
fill_median source column=column_name as alias
```

**Examples**:
```noeta
fill_median sales column=price as median_filled
fill_median data column=income as med_filled
```

---

### 78. FILL MODE
**Purpose**: Fill missing values with column mode

**Syntax**:
```noeta
fill_mode source column=column_name as alias
```

**Examples**:
```noeta
fill_mode sales column=category as mode_filled
fill_mode data column=status as most_common_filled
```

---

### 79. INTERPOLATE
**Purpose**: Interpolate missing values

**Syntax**:
```noeta
interpolate source method="linear" as alias
```

**Parameters**:
- `method` (optional): Interpolation method ("linear", "polynomial", "spline")

**Examples**:
```noeta
interpolate sales method="linear" as interpolated
interpolate data method="polynomial" order=2 as smooth
```

---

### 80. DROP DUPLICATES
**Purpose**: Remove duplicate rows

**Syntax**:
```noeta
drop_duplicates source as alias
drop_duplicates source columns=[col1, col2] keep="first" as alias
```

**Parameters**:
- `columns` (optional): Columns to consider for duplicates
- `keep` (optional): Which duplicate to keep ("first", "last", false), default="first"

**Examples**:
```noeta
drop_duplicates sales as unique_sales
drop_duplicates data columns=[id] keep="last" as deduplicated
```

---

### 81. REPLACE
**Purpose**: Replace specific values

**Syntax**:
```noeta
replace source column=column_name old=value new=value as alias
```

**Parameters**:
- `old` (required): Value to replace
- `new` (required): Replacement value

**Examples**:
```noeta
replace sales column=status old="pending" new="processing" as updated
replace data column=category old=null new="Unknown" as cleaned
```

---

### 82. CLIP
**Purpose**: Clip values to specified range

**Syntax**:
```noeta
clip source column=column_name lower=min upper=max as alias
```

**Parameters**:
- `lower` (optional): Minimum value
- `upper` (optional): Maximum value

**Examples**:
```noeta
clip sales column=quantity lower=0 upper=100 as clipped_qty
clip data column=age lower=0 upper=120 as valid_age
```

---

### 83. ISNULL
**Purpose**: Detect missing values

**Syntax**:
```noeta
isnull source column=column_name as alias
```

**Examples**:
```noeta
isnull sales column=discount as missing_discount
isnull data column=email as no_email
```

---

### 84. NOTNULL
**Purpose**: Detect non-missing values

**Syntax**:
```noeta
notnull source column=column_name as alias
```

**Examples**:
```noeta
notnull sales column=discount as has_discount
notnull data column=email as has_email
```

---

## Data Reshaping

### 85. PIVOT
**Purpose**: Reshape data from long to wide format

**Syntax**:
```noeta
pivot source index=index_col columns=column_col values=value_col as alias
```

**Parameters**:
- `index` (required): Column to use as index
- `columns` (required): Column to pivot on
- `values` (required): Column with values

**Examples**:
```noeta
pivot sales index=date columns=category values=revenue as wide_sales
pivot data index=id columns=metric values=value as metrics_wide
```

---

### 86. MELT
**Purpose**: Reshape data from wide to long format

**Syntax**:
```noeta
melt source id_vars=[col1] value_vars=[col2, col3] as alias
```

**Parameters**:
- `id_vars` (required): Columns to keep as identifiers
- `value_vars` (optional): Columns to unpivot
- `var_name` (optional): Name for variable column
- `value_name` (optional): Name for value column

**Examples**:
```noeta
melt sales id_vars=[date] value_vars=[product1, product2] as long_sales
melt data id_vars=[id, name] as melted
```

---

### 87. STACK
**Purpose**: Stack columns into rows (pivot column index to row)

**Syntax**:
```noeta
stack source as alias
```

**Examples**:
```noeta
stack sales as stacked
stack wide_data as long_format
```

---

### 88. UNSTACK
**Purpose**: Unstack rows into columns (pivot row index to column)

**Syntax**:
```noeta
unstack source as alias
unstack source level=0 as alias
```

**Parameters**:
- `level` (optional): Index level to unstack

**Examples**:
```noeta
unstack sales as unstacked
unstack multiindex_data level=1 as wide
```

---

### 89. TRANSPOSE
**Purpose**: Swap rows and columns

**Syntax**:
```noeta
transpose source as alias
```

**Examples**:
```noeta
transpose sales as transposed
transpose matrix_data as flipped
```

---

### 90. EXPLODE
**Purpose**: Expand list-like values into separate rows

**Syntax**:
```noeta
explode source column=column_name as alias
```

**Examples**:
```noeta
explode sales column=tags as exploded_tags
explode data column=items as item_rows
```

---

### 91. NORMALIZE
**Purpose**: Normalize nested JSON structures

**Syntax**:
```noeta
normalize source column=column_name as alias
```

**Examples**:
```noeta
normalize sales column=metadata as flat_metadata
normalize json_data column=nested_obj as flattened
```

---

## Data Combining

### 92. JOIN
**Purpose**: Join two DataFrames on common column

**Syntax**:
```noeta
join left with right on column_name as alias
join left with right on column_name how="inner" as alias
```

**Parameters**:
- `on` (required): Column to join on
- `how` (optional): Join type ("inner", "left", "right", "outer"), default="inner"

**Examples**:
```noeta
join sales with customers on customer_id as sales_customers
join orders with products on product_id how="left" as order_details
```

---

### 93. MERGE
**Purpose**: Advanced merge with multiple options

**Syntax**:
```noeta
merge left with right left_on=col1 right_on=col2 how="inner" as alias
```

**Parameters**:
- `left_on` (required): Left DataFrame column
- `right_on` (required): Right DataFrame column
- `how` (optional): Merge type
- `suffixes` (optional): Suffixes for overlapping columns

**Examples**:
```noeta
merge sales with customers left_on=cust_id right_on=id as merged
merge orders with products left_on=prod right_on=product_id how="left" as combined
```

---

### 94. CONCAT VERTICAL
**Purpose**: Stack DataFrames vertically (append rows)

**Syntax**:
```noeta
concat_vertical df1 with df2 as alias
```

**Parameters**:
- `ignore_index` (optional): Reset index, default=false

**Examples**:
```noeta
concat_vertical jan_sales with feb_sales as q1_sales
concat_vertical data1 with data2 ignore_index=true as combined
```

---

### 95. CONCAT HORIZONTAL
**Purpose**: Stack DataFrames horizontally (append columns)

**Syntax**:
```noeta
concat_horizontal df1 with df2 as alias
```

**Examples**:
```noeta
concat_horizontal features with targets as dataset
concat_horizontal data1 with data2 as wide_data
```

---

### 96. APPEND
**Purpose**: Append rows to DataFrame

**Syntax**:
```noeta
append source with other as alias
```

**Examples**:
```noeta
append existing_data with new_data as updated
append main with additional ignore_index=true as combined
```

---

### 97. CROSS JOIN
**Purpose**: Cartesian product of two DataFrames

**Syntax**:
```noeta
cross_join df1 with df2 as alias
```

**Examples**:
```noeta
cross_join products with stores as product_store_combinations
cross_join options1 with options2 as all_combinations
```

---

## Aggregation Operations

### 98. GROUPBY
**Purpose**: Group data and apply aggregations

**Syntax**:
```noeta
groupby source by {col1, col2} compute {sum: value_col} as alias
groupby source by {col1} compute {sum: col1, avg: col2, count: col3} as alias
```

**Parameters**:
- `by` (required): Columns to group by
- `compute` (required): Aggregation functions and columns

**Aggregation Functions**: sum, avg, mean, count, min, max, std, var, median, first, last, nunique

**Examples**:
```noeta
groupby sales by {category} compute {sum: quantity} as category_totals
groupby data by {region, product} compute {avg: price, count: id} as summary
```

---

### 99. AGG
**Purpose**: Apply multiple aggregations

**Syntax**:
```noeta
agg source functions={col1: ["sum", "mean"], col2: ["max"]} as alias
```

**Examples**:
```noeta
agg sales functions={revenue: ["sum", "mean"], quantity: ["max", "min"]} as stats
```

---

### 100. SUM
**Purpose**: Calculate sum

**Syntax**:
```noeta
sum source column=column_name as alias
```

**Examples**:
```noeta
sum sales column=revenue as total_revenue
sum data column=quantity as total_qty
```

---

### 101. MEAN / AVG
**Purpose**: Calculate mean/average

**Syntax**:
```noeta
mean source column=column_name as alias
avg source column=column_name as alias
```

**Examples**:
```noeta
mean sales column=price as avg_price
avg data column=age as average_age
```

---

### 102. MEDIAN
**Purpose**: Calculate median

**Syntax**:
```noeta
median source column=column_name as alias
```

**Examples**:
```noeta
median sales column=price as median_price
median data column=income as median_income
```

---

### 103. MIN
**Purpose**: Find minimum value

**Syntax**:
```noeta
min source column=column_name as alias
```

**Examples**:
```noeta
min sales column=price as min_price
min data column=temperature as lowest_temp
```

---

### 104. MAX
**Purpose**: Find maximum value

**Syntax**:
```noeta
max source column=column_name as alias
```

**Examples**:
```noeta
max sales column=price as max_price
max data column=temperature as highest_temp
```

---

### 105. COUNT
**Purpose**: Count non-null values

**Syntax**:
```noeta
count source column=column_name as alias
```

**Examples**:
```noeta
count sales column=order_id as order_count
count data column=valid_entries as entry_count
```

---

### 106. STD
**Purpose**: Calculate standard deviation

**Syntax**:
```noeta
std source column=column_name as alias
```

**Examples**:
```noeta
std sales column=price as price_std
std data column=value as std_dev
```

---

### 107. VAR
**Purpose**: Calculate variance

**Syntax**:
```noeta
var source column=column_name as alias
```

**Examples**:
```noeta
var sales column=price as price_variance
var data column=value as variance
```

---

### 108. FIRST
**Purpose**: Get first value in group

**Syntax**:
```noeta
first source column=column_name as alias
```

**Examples**:
```noeta
groupby sales by {customer_id} compute {first: order_date} as first_orders
```

---

### 109. LAST
**Purpose**: Get last value in group

**Syntax**:
```noeta
last source column=column_name as alias
```

**Examples**:
```noeta
groupby sales by {customer_id} compute {last: order_date} as last_orders
```

---

### 110. NTH
**Purpose**: Get nth value in group

**Syntax**:
```noeta
nth source n=0 column=column_name as alias
```

**Parameters**:
- `n` (required): Position (0-indexed)

**Examples**:
```noeta
groupby sales by {category} compute {nth: price, n: 2} as third_prices
```

---

### 111. NUNIQUE
**Purpose**: Count unique values

**Syntax**:
```noeta
nunique source column=column_name as alias
```

**Examples**:
```noeta
nunique sales column=customer_id as unique_customers
nunique data column=category as category_count
```

---

### 112. QUANTILE
**Purpose**: Calculate quantile/percentile

**Syntax**:
```noeta
quantile source column=column_name q=0.5 as alias
```

**Parameters**:
- `q` (required): Quantile to compute (0-1)

**Examples**:
```noeta
quantile sales column=price q=0.95 as price_95th
quantile data column=age q=0.25 as age_q1
```

---

### 113. ROLLING
**Purpose**: Apply rolling window aggregation

**Syntax**:
```noeta
rolling source column=column_name window=7 function="mean" as alias
```

**Parameters**:
- `window` (required): Window size
- `function` (required): Aggregation function ("mean", "sum", "min", "max", "std")

**Examples**:
```noeta
rolling sales column=revenue window=7 function="mean" as weekly_avg
rolling data column=value window=3 function="sum" as rolling_sum
```

---

### 114. EXPANDING
**Purpose**: Apply expanding window aggregation

**Syntax**:
```noeta
expanding source column=column_name function="sum" as alias
```

**Parameters**:
- `function` (required): Aggregation function

**Examples**:
```noeta
expanding sales column=revenue function="sum" as cumulative_revenue
expanding data column=value function="mean" as running_average
```

---

### 115. PIVOT TABLE
**Purpose**: Create pivot table with aggregation

**Syntax**:
```noeta
pivot_table source index=row_col columns=col_col values=val_col aggfunc="sum" as alias
```

**Parameters**:
- `aggfunc` (required): Aggregation function

**Examples**:
```noeta
pivot_table sales index=date columns=category values=revenue aggfunc="sum" as pivot
```

---

### 116. CROSSTAB
**Purpose**: Compute cross-tabulation

**Syntax**:
```noeta
crosstab source rows=col1 columns=col2 as alias
```

**Examples**:
```noeta
crosstab sales rows=region columns=category as cross_table
```

---

### 117. VALUE COUNTS
**Purpose**: Count occurrences of unique values

**Syntax**:
```noeta
value_counts source column=column_name as alias
```

**Parameters**:
- `normalize` (optional): Return proportions instead of counts
- `sort` (optional): Sort by frequency

**Examples**:
```noeta
value_counts sales column=category as category_counts
value_counts data column=status normalize=true as status_proportions
```

---

## Window Functions

### 118. RANK
**Purpose**: Assign ranks to values

**Syntax**:
```noeta
rank source column=column_name method="average" as alias
```

**Parameters**:
- `method` (optional): Ranking method ("average", "min", "max", "first", "dense")
- `ascending` (optional): Sort order, default=true

**Examples**:
```noeta
rank sales column=revenue method="dense" as revenue_rank
rank data column=score ascending=false as rank_desc
```

---

### 119. DENSE RANK
**Purpose**: Assign dense ranks (no gaps)

**Syntax**:
```noeta
dense_rank source column=column_name as alias
```

**Examples**:
```noeta
dense_rank sales column=revenue as dense_revenue_rank
```

---

### 120. ROW NUMBER
**Purpose**: Assign sequential row numbers

**Syntax**:
```noeta
row_number source as alias
row_number source partition_by=[col1] as alias
```

**Parameters**:
- `partition_by` (optional): Columns to partition by

**Examples**:
```noeta
row_number sales as numbered
row_number sales partition_by=[category] as category_row_num
```

---

### 121. PERCENT RANK
**Purpose**: Calculate percentile rank

**Syntax**:
```noeta
percent_rank source column=column_name as alias
```

**Examples**:
```noeta
percent_rank sales column=revenue as revenue_percentile
```

---

### 122. NTILE
**Purpose**: Divide into N equal-sized buckets

**Syntax**:
```noeta
ntile source column=column_name n=4 as alias
```

**Parameters**:
- `n` (required): Number of buckets

**Examples**:
```noeta
ntile sales column=revenue n=4 as quartile
ntile data column=score n=10 as decile
```

---

### 123. LAG
**Purpose**: Access previous row value

**Syntax**:
```noeta
lag source column=column_name periods=1 as alias
```

**Parameters**:
- `periods` (optional): Number of rows to shift, default=1

**Examples**:
```noeta
lag sales column=price periods=1 as prev_price
lag data column=value periods=7 as week_ago
```

---

### 124. LEAD
**Purpose**: Access next row value

**Syntax**:
```noeta
lead source column=column_name periods=1 as alias
```

**Parameters**:
- `periods` (optional): Number of rows to shift, default=1

**Examples**:
```noeta
lead sales column=price periods=1 as next_price
lead data column=value periods=7 as week_ahead
```

---

### 125. CUMSUM
**Purpose**: Calculate cumulative sum

**Syntax**:
```noeta
cumsum source column=column_name as alias
```

**Examples**:
```noeta
cumsum sales column=revenue as running_total
cumsum data column=quantity as cumulative_qty
```

---

### 126. CUMMAX
**Purpose**: Calculate cumulative maximum

**Syntax**:
```noeta
cummax source column=column_name as alias
```

**Examples**:
```noeta
cummax sales column=price as all_time_high
cummax data column=temperature as max_so_far
```

---

### 127. CUMMIN
**Purpose**: Calculate cumulative minimum

**Syntax**:
```noeta
cummin source column=column_name as alias
```

**Examples**:
```noeta
cummin sales column=price as all_time_low
cummin data column=temperature as min_so_far
```

---

### 128. CUMPROD
**Purpose**: Calculate cumulative product

**Syntax**:
```noeta
cumprod source column=column_name as alias
```

**Examples**:
```noeta
cumprod sales column=growth_rate as compound_growth
cumprod data column=multiplier as cumulative_product
```

---

### 129. CUMCOUNT
**Purpose**: Count cumulative occurrences within groups

**Syntax**:
```noeta
cumcount source as alias
```

**Examples**:
```noeta
groupby sales by {customer_id} cumcount as order_sequence
```

---

### 130. WINDOW SUM
**Purpose**: Calculate window sum

**Syntax**:
```noeta
window_sum source column=column_name window=3 as alias
```

**Examples**:
```noeta
window_sum sales column=revenue window=7 as weekly_sum
```

---

### 131. WINDOW AVG
**Purpose**: Calculate window average

**Syntax**:
```noeta
window_avg source column=column_name window=3 as alias
```

**Examples**:
```noeta
window_avg sales column=price window=5 as five_day_avg
```

---

## Index Operations

### 132. SET INDEX
**Purpose**: Set column(s) as index

**Syntax**:
```noeta
set_index source columns=[col1, col2] as alias
```

**Parameters**:
- `drop` (optional): Drop column after setting as index, default=true

**Examples**:
```noeta
set_index sales columns=[date] as indexed_sales
set_index data columns=[id] drop=false as with_index
```

---

### 133. RESET INDEX
**Purpose**: Reset index to default integer index

**Syntax**:
```noeta
reset_index source as alias
```

**Parameters**:
- `drop` (optional): Drop the old index, default=false

**Examples**:
```noeta
reset_index sales as reset_sales
reset_index data drop=true as clean_reset
```

---

### 134. SORT INDEX
**Purpose**: Sort by index

**Syntax**:
```noeta
sort_index source ascending=true as alias
```

**Parameters**:
- `ascending` (optional): Sort order, default=true

**Examples**:
```noeta
sort_index sales as sorted_by_index
sort_index data ascending=false as reverse_sorted
```

---

### 135. REINDEX
**Purpose**: Conform DataFrame to new index

**Syntax**:
```noeta
reindex source index=[val1, val2, val3] as alias
```

**Parameters**:
- `index` (required): New index values
- `fill_value` (optional): Value for missing entries

**Examples**:
```noeta
reindex sales index=[0, 1, 2, 3, 4] fill_value=0 as reindexed
```

---

### 136. SET MULTIINDEX
**Purpose**: Set multiple columns as hierarchical index

**Syntax**:
```noeta
set_multiindex source columns=[col1, col2] as alias
```

**Examples**:
```noeta
set_multiindex sales columns=[region, category] as hierarchical
```

---

## Statistical Operations

### 137. DESCRIBE
**Purpose**: Generate descriptive statistics

**Syntax**:
```noeta
describe source
describe source columns=[col1, col2]
```

**Parameters**:
- `columns` (optional): Specific columns to describe

**Output**: count, mean, std, min, 25%, 50%, 75%, max

**Examples**:
```noeta
describe sales
describe data columns=[price, quantity]
```

---

### 138. SUMMARY
**Purpose**: Display dataset summary (shape, columns, types, missing values)

**Syntax**:
```noeta
summary source
```

**Examples**:
```noeta
summary sales
summary data
```

---

### 139. INFO
**Purpose**: Display concise DataFrame information

**Syntax**:
```noeta
info source
```

**Examples**:
```noeta
info sales
info data
```

---

### 140. CORR
**Purpose**: Calculate correlation matrix

**Syntax**:
```noeta
corr source method="pearson" as alias
```

**Parameters**:
- `method` (optional): Correlation method ("pearson", "spearman", "kendall")

**Examples**:
```noeta
corr sales method="pearson" as correlations
corr data method="spearman" as rank_correlations
```

---

### 141. COV
**Purpose**: Calculate covariance matrix

**Syntax**:
```noeta
cov source as alias
```

**Examples**:
```noeta
cov sales as covariances
cov data as cov_matrix
```

---

### 142. UNIQUE
**Purpose**: Get unique values

**Syntax**:
```noeta
unique source column=column_name as alias
```

**Examples**:
```noeta
unique sales column=category as categories
unique data column=status as statuses
```

---

### 143. SAMPLE
**Purpose**: Random sample of rows

**Syntax**:
```noeta
sample source n=10 as alias
sample source frac=0.1 as alias
```

**Parameters**:
- `n` (optional): Number of rows
- `frac` (optional): Fraction of rows (0-1)
- `random_state` (optional): Random seed

**Examples**:
```noeta
sample sales n=100 as sample_100
sample data frac=0.1 random_state=42 as ten_percent
```

---

### 144. BINNING
**Purpose**: Discretize continuous values into bins

**Syntax**:
```noeta
binning source column=column_name bins=5 as alias
```

**Parameters**:
- `bins` (required): Number of bins or bin edges
- `labels` (optional): Labels for bins

**Examples**:
```noeta
binning sales column=price bins=5 as price_categories
binning data column=age bins=[0, 18, 65, 100] labels=["child", "adult", "senior"] as age_groups
```

---

### 145. CUT
**Purpose**: Bin values with explicit boundaries

**Syntax**:
```noeta
cut source column=column_name bins=[0, 50, 100, 150] labels=["low", "mid", "high"] as alias
```

**Parameters**:
- `bins` (required): Bin edges
- `labels` (optional): Labels for bins
- `include_lowest` (optional): Include left edge, default=false

**Examples**:
```noeta
cut sales column=price bins=[0, 50, 100, 150] labels=["cheap", "moderate", "expensive"] as price_category
cut data column=score bins=[0, 60, 80, 100] labels=["F", "C", "A"] as grade
```

---

## Visualization Operations

### 146. BOXPLOT
**Purpose**: Create box plot

**Syntax**:
```noeta
boxplot source columns {col1, col2}
```

**Parameters**:
- `columns` (required): Columns to visualize

**Examples**:
```noeta
boxplot sales columns {price, quantity}
boxplot data columns {value1, value2, value3}
```

---

### 147. HEATMAP
**Purpose**: Create correlation heatmap

**Syntax**:
```noeta
heatmap source columns {col1, col2, col3}
```

**Parameters**:
- `columns` (required): Numeric columns

**Examples**:
```noeta
heatmap sales columns {price, quantity, discount}
heatmap data columns {feature1, feature2, feature3}
```

---

### 148. PAIRPLOT
**Purpose**: Create pairwise scatter plots

**Syntax**:
```noeta
pairplot source columns {col1, col2, col3}
```

**Parameters**:
- `columns` (required): Columns to plot

**Examples**:
```noeta
pairplot sales columns {price, quantity, revenue}
pairplot data columns {x, y, z}
```

---

### 149. TIMESERIES
**Purpose**: Create time series line plot

**Syntax**:
```noeta
timeseries source x=date_column y=value_column
```

**Parameters**:
- `x` (required): Time/date column
- `y` (required): Value column

**Examples**:
```noeta
timeseries sales x=date y=revenue
timeseries data x=timestamp y=value
```

---

### 150. PIE
**Purpose**: Create pie chart

**Syntax**:
```noeta
pie source values=values_column labels=labels_column
```

**Parameters**:
- `values` (required): Numeric values
- `labels` (required): Category labels

**Examples**:
```noeta
pie category_totals values=quantity_sum labels=category
pie data values=count labels=status
```

---

## Apply/Map Operations

### 151. APPLY
**Purpose**: Apply function to column(s)

**Syntax**:
```noeta
apply source columns=[col1] function="x * 2" as alias
```

**Parameters**:
- `columns` (required): Columns to transform
- `function` (required): Expression or function (use `x` for column value)

**Examples**:
```noeta
apply sales columns=[price] function="x * 1.1" as price_with_tax
apply data columns=[value] function="x ** 2" as squared
```

---

### 152. APPLYMAP
**Purpose**: Apply function element-wise to entire DataFrame

**Syntax**:
```noeta
applymap source function="lambda x: x * 2" as alias
```

**Parameters**:
- `function` (required): Function to apply to each element

**Examples**:
```noeta
applymap sales function="lambda x: x * 2 if isinstance(x, (int, float)) else x" as doubled
applymap data function="lambda x: str(x).upper() if isinstance(x, str) else x" as uppercase
```

---

### 153. MAP VALUES
**Purpose**: Map values using dictionary

**Syntax**:
```noeta
map_values source column=column_name mapping={old1: new1, old2: new2} as alias
```

**Parameters**:
- `column` (required): Column to map
- `mapping` (required): Dictionary of old->new values

**Examples**:
```noeta
map_values sales column=status mapping={"P": "Pending", "C": "Complete"} as readable_status
map_values data column=code mapping={1: "Low", 2: "Medium", 3: "High"} as priority
```

---

### 154. TRANSFORM
**Purpose**: Transform within groups

**Syntax**:
```noeta
transform source column=column_name function="mean" as alias
```

**Parameters**:
- `function` (required): Transformation function

**Examples**:
```noeta
groupby sales by {category} transform column=price function="mean" as category_avg_price
```

---

## Cumulative Operations

### 155. CUMSUM
**Purpose**: Cumulative sum (see Window Functions #125)

### 156. CUMMAX
**Purpose**: Cumulative maximum (see Window Functions #126)

### 157. CUMMIN
**Purpose**: Cumulative minimum (see Window Functions #127)

### 158. CUMPROD
**Purpose**: Cumulative product (see Window Functions #128)

---

## Time Series Operations

### 159. PCT CHANGE
**Purpose**: Calculate percentage change

**Syntax**:
```noeta
pct_change source column=column_name periods=1 as alias
```

**Parameters**:
- `periods` (optional): Number of periods to shift, default=1

**Examples**:
```noeta
pct_change sales column=price periods=1 as price_change_pct
pct_change data column=value periods=7 as weekly_change
```

---

### 160. DIFF
**Purpose**: Calculate difference between consecutive values

**Syntax**:
```noeta
diff source column=column_name periods=1 as alias
```

**Parameters**:
- `periods` (optional): Number of periods to shift, default=1

**Examples**:
```noeta
diff sales column=revenue periods=1 as revenue_diff
diff data column=value periods=7 as weekly_diff
```

---

### 161. SHIFT
**Purpose**: Shift values by specified periods

**Syntax**:
```noeta
shift source column=column_name periods=1 as alias
```

**Parameters**:
- `periods` (required): Number of periods to shift (positive=down, negative=up)

**Examples**:
```noeta
shift sales column=price periods=1 as prev_price
shift data column=value periods=-1 as next_value
```

---

## Data Validation

### 162. ASSERT UNIQUE
**Purpose**: Validate uniqueness constraint

**Syntax**:
```noeta
assert_unique source column=column_name
```

**Parameters**:
- `column` (required): Column to check for uniqueness

**Examples**:
```noeta
assert_unique sales column=order_id
assert_unique users column=email
```

---

### 163. ASSERT NO NULLS
**Purpose**: Validate no missing values

**Syntax**:
```noeta
assert_no_nulls source column=column_name
```

**Parameters**:
- `column` (required): Column to check for nulls

**Examples**:
```noeta
assert_no_nulls sales column=price
assert_no_nulls data column=required_field
```

---

### 164. ASSERT RANGE
**Purpose**: Validate values within range

**Syntax**:
```noeta
assert_range source column=column_name min=0 max=100
```

**Parameters**:
- `column` (required): Column to check
- `min` (optional): Minimum value
- `max` (optional): Maximum value

**Examples**:
```noeta
assert_range sales column=quantity min=0 max=1000
assert_range data column=age min=0 max=120
```

---

## Boolean Operations

### 165. ANY
**Purpose**: Check if any value is True

**Syntax**:
```noeta
any source column=column_name as alias
```

**Examples**:
```noeta
any sales column=has_discount as any_discount
```

---

### 166. ALL
**Purpose**: Check if all values are True

**Syntax**:
```noeta
all source column=column_name as alias
```

**Examples**:
```noeta
all sales column=is_valid as all_valid
```

---

### 167. COUNT TRUE
**Purpose**: Count True values

**Syntax**:
```noeta
count_true source column=column_name as alias
```

**Examples**:
```noeta
count_true sales column=is_premium as premium_count
```

---

## Binning Operations

### 168. BINNING
**Purpose**: See Statistical Operations #144

### 169. CUT
**Purpose**: See Statistical Operations #145

---

## Conversion Instructions

To convert this markdown file to Microsoft Word (.doc/.docx):

### Method 1: Using Microsoft Word
1. Open Microsoft Word
2. File → Open → Select this .md file
3. File → Save As → Choose "Word Document (.docx)"

### Method 2: Using Google Docs
1. Upload this file to Google Drive
2. Open with Google Docs
3. File → Download → Microsoft Word (.docx)

### Method 3: Using Pandoc (Command Line)
```bash
pandoc NOETA_FUNCTIONS_REFERENCE.md -o NOETA_FUNCTIONS_REFERENCE.docx
```

### Method 4: Using Online Converter
- Visit: https://cloudconvert.com/md-to-docx
- Upload this file and convert

---

## Quick Reference Summary

**Total Functions**: 167 operations across 22 categories

**Most Common Operations**:
- Data Loading: `load csv`, `load json`, `load excel`, `load parquet`
- Selection: `select`, `head`, `tail`, `filter`
- Cleaning: `dropna`, `fillna`, `drop_duplicates`
- Transformation: `groupby`, `join`, `pivot`, `melt`
- Analysis: `describe`, `summary`, `corr`

**Getting Started**:
```noeta
# 1. Load data
load csv "data.csv" as df

# 2. Explore
describe df
summary df

# 3. Clean
dropna df as clean
drop_duplicates clean as unique

# 4. Transform
groupby unique by {category} compute {sum: value} as summary

# 5. Save
save summary to "output.csv"
```

---

**Document Version**: 2.0  
**Last Updated**: December 19, 2025  
**For**: Noeta DSL v2.0  
**Status**: Production Ready ✅

