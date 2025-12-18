################################################################################

Pre-Processing:

################################################################################


  Load in Packages necessary for Pre-Processing
################################################################################
### Step 1 : Import Initial Data

  Load SC Data as a CSV
  
  Load Streamflow Data as a csv
  
  Load Precipitation SC Data as a csv

  Isolate Precipitation SC (Rain_SC) Dataframe (df) Datetime and SC columns
  
  Trim Rain_SC df Datetime range to match  SC & Streamflow Datetime ranges

################################################################################
### Step 1A : Create un-stitched datetime dataframe
Note: For high resolution datasets (ie 15 minute resolution), unstitching of the streamflow, SC, & Precipitation SC may be necessary)

  Set start time for un-stitched Datetime dataframe
  
  Set end time for un-stitched Datetime dataframe

  Generate 15-minute interval Datetime sequence

  Create Datetime dataframe (df) from datetime_seq
  
  Rename Datetime column
  
  Mutate Datetime df to include timezone for later merging

  Clean up Datetime df after timezone inclusion

  Generate copy of Datetime df

################################################################################
### Step 2 : Match format of dataframes

  For SC, Streamflow, and Rain_SC Data, assign time stamps and convert to xts object

SC:

  Create & format POSIXct column for SC data
  
  Combine POSIXct with df_SC Timezone & SC columns
  
  Rename columns in final SC df

Water Level (strflow):

  Create & format POSIXct column for Streamflow data
  
  Combine POSIXct with df_WL Timezone & Streamflow columns
  
  Rename columns in final Streamflow df

SC Rain (Precipitation SC):

  Create & format POSIXct column for Rain SC data
  
  Combine POSIXct with MTN Streamflow columns
  
  Rename columns in final Streamflow df

################################################################################
### Step 2A : Merging & Trimming

  Convert Datetime, Streamflow, SC, and Rain SC datetime columns to characters 
  
  Verify conversion to characters

  Combine Datetime, Streamflow, and SC dfs as a list (dfr)
  
  Merge all dfs in  the list by datetime and timezone 
  
  Check how column 1 of merged_df and df are read

  Combine df with SC rain data

  Merge dfr data
  
  Clean up ts to exclude timezone (Timezone only necessary for high resolution datetime merging)
  
  Add index column to ts

  Convert ts to xts object & convert datetime to metadata

################################################################################
### Step 3 : Inspect for missing data & clear environment except for ts

  Inspect for Missing Streamflow, SC, & SC_rain data
  
  Preserve only ts in environment

################################################################################
### Step 3A : Identification of years for recession analysis

  Identify at least 5 years
  
  Confirm year selections by plotting Streamflow with selected years highlighted

### Step 3B : Manual recession limb identification per selected year 

  Identify recession limb for each selected year (Typically the equivalent of 75 - 100 days)

### Step 3C : Identify recession limb for each peak

  Identify Range of x values for the recession

  Normal scale of each recession period
  
  Logarithm scale of each recession period

### Step 3D : Perform linear regression analysis to calculate regression coefficient for each peak  

  For each recession period, record the result of the regression
  
  Identify the slope of the curve, coefficient of determination, & Alpha value (Recession constant value per selected year)

### Step 3E : Post-Recession analysis cleanup

  Complile calculated recession constant values into a list (dfa)

  Delete temporary index column in ts used for recession constant analysis
  
  Create Streamflow, SC, and Rain SC xts objects from ts
  
  Trim ts to match length of Daily Resolution Dataset

  Clear Environment of everything except ts for Processing & Post-Processing

  Create ts in dataframe form for use alongside ts (xts object) in Processing

################################################################################
### Step 3F : Missing Data Interpolation

  Missing Strflow Interpolation via pchip interpolation
  
  Missing SC Interpolation via pchip interpolation

################################################################################
### Step 3G : Tidy up Environment. Reduce to just ts, ts_int, & dfa

  Clear Environment of everything except the list of recession constant and both iterations of the data (ts & ts_int) for Processing


################################################################################

Processing:

################################################################################




















\
