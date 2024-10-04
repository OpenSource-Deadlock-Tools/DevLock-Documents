# Data Labeling Standards

A standardized format will ensure we can adequately track and leverage the data we receive from multiple sources and contributors. This document outlines how to handle raw data input to ensure that any processing can sufficiently identify and process the data with minimal chance of introducing malformed parsing results.

## Standard File Names
All input data should thus follow the following format:

```
{SCHEMA}.{ANY}.TXXX.{EXT}
```

- `SCHEMA`
  - The schema used to deserialize the data
- `ANY`
  - A unique identifier (ex. match ID)
- `TXXX`
  - A generated unique identifier, based on the data source type and version to ensure we can identify any file regardless of any other malformed labeling
- `EXT`
  - The file extension (ex. `.json`, `.bin`)
 
The most important aspect of the standard file name format is the unique source identifier. We can use it to track any details about the source file, such as what process generated that data for bug fixes.

Data sources must all be trusted before they are accepted into the pipeline. Part of this procedure will be assigning each one a unique identifier that the source will use when generating its filename.

All data should be identifiable regardless of data structure through its unique ID and schema, irrespective of the directory structure. Directories are purely for human organizational purposes, so relying on them to organize data during transformations is discouraged.
