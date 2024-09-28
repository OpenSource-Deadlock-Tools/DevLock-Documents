# Data Labeling Standards

In order to make sure we are able to properly track and leverage the data we receive from multiple sources and contributors, having a standardized format will be important.

This document is to outline the handling of raw data input, so as to ensure that any processing is able to adequetly identify and process the data with minimal chance to introduce malformed parsing results.

## Standard File Names
All input data should thus follow the following format 

{SCHEMA}.{ANY}.TXXX.{EXT}

- SCHEMA
  - The schema that can be used to deserialize the data
- ANY
  - Any sort of unique identifier, for example a match ID the data is from
- TXXX
  - A unique identifier that will be generated based of the data source type and version, such that we can adequetly identify any file regardless of any other malformed labeling
- EXT
  - The file extension, such as .json, .bin and so forth
 
The most important aspect of this format is the unique source identifer. This will be used to track any details about the source file, such as what process generated that piece of data, and any issues that are identified at a later point that need to be addressed.

Since data sources are all going to have to be trusted before they are accepted into the pipeline, part of this procedure will be to first assign a unique identifier that the source will use when generating it's filename.

Directory structure can be used as needed, but all data should be identifiable regardless of data structure through the use of it's uniqueID and schema, meaning that directories are purely for human organizational purposes, and should be discouraged as part of organizing data during transformations.
