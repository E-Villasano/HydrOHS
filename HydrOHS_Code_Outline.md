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

### Step 3H: Load in additional packages necessary for Processing & Post-Processing 

Load in WH, plotly, & lineq packages

Begin excecution of HydrOHS base function

  ################################################################################
  ### Step 4 : Compute SCy from smoothed SC_rain

  Create sub-dataframe of just SC_rain
  
  Determine SCy interpolation type
  
  Remove any remaining na's in streamflow
  
  ################################################################################
  ### Step 5 : Apply Eckhardt RDF and calculate bf and qf
  
  Create dfl
  
  Define Recession Constant and BFImax values

  Create empty b vector
  
  fill in initial value of b

  iterate through remaining values
  
  assign b
  
  calculate y
  
  calculate bfi
  
  #Assigns 0 streamflow days a negative bfi for later removal
  
  round bfi to six digits (mitigates potential error in later peak identification)

  ################################################################################
  ### Step 6 : Tune SCy
  
  Omit na's present in ts and dfl via the warmup and cooldown periods after Eckhardt application
  
  Amplify, smooth and offset SC_rain
  
  Convert ts to xts object

  ################################################################################
  ### Step 7 : set SCy lag ON XTS DATA!!!
  
  Append SCy column
  
  Omit NAs in dfl

  ################################################################################
  ### Step 8 : Filter out non-perennial days & re-index
  
  Filter out non-perennial days
  
  Re-index (create artificial, truncated ts only for comparison of SC and BFI trends) & rename dataframe (dfl to dfl_filt)
  
  ################################################################################
  ### Step 9 : Get bfi peaks based on peak height (ph) and peak length (pl) adjustable variables
  
  Identify minpeakheight & minpeakdistance valyes (rounded to mitigate findpeak point difference errors)
  
  Identify bfi peaks
  
  Subset dfl at BFI peak indices
  
  ################################################################################
  ### Step 10 : Get SCb for peaks
  
  Populate SCb sub-dataframe with reference to dflp values
  
  Relabel columns in SCb
  
  Check for negative indices in SCb & set to 0 as needed

  Apply SCb to dflp as the SCb column

  
  ################################################################################
  ### Step 11 : Interpolate for entire SCb

  Using peaks identified in dflp/SCb for reference, interpolate SCb for the remaining rows

  Remove any remaining NA's 
  
  ################################################################################
  ### Step 12 : Chemical Mass Balance
  
  Chemical mass balance: Use the estimated SCb, b, SCy & y in conjunction with measured Q to calculate final SCq (modeled streamflow SC) estimations
  
  ################################################################################
  ### Step 13 : Extract calibration data and merge

  #Use finalized dataframe to generate a calibration dataframe (cali)

  ################################################################################
  ### Step 14 : Assign mass balance indicators

  ################################################################################
  ### Step 14A : Apply heteroscedasticity penalty 
  
  Compute residuals
  
  ################################################################################
  ### Step 15 : Objective functions

  Apply safeguard conditions

  Assign a High error penalty if safeguard conditions are violated in attempt to satisfy the four objective functions

  Establish objective functions:
 
  F1: RMSE between measured and modeled SC
    
  F2 &F3: Mass balance indicators (sign agreement)

  F4: BFImax value must correlate with maximum SCb value in dflp

End of HydrOHS base function

################################################################################
Multi- Objective Optimization Execution

Batch-safe wrapper (guarantees k×3 matrix for ecr):

Establish Bounds / dimensions (from your earlier setup):

Set upper & lower bounds for all 11 decision variables

Parent Population Size

Offspring Population Size

generate a "printer" terminator that fires every generation


CHUNK 1: run and save survivors (seed)

Establish dimensions  &  number of generations of the algorithm's run for execution & evaluation via NSGA-II

Generate list of decision vectors

saveRDS(seed, "seed_nsga2_chunk1.rds")

saveRDS(res_1, "res_1_nsga2_chunk1.rds")

Inspect results

Generate matrix of 4 objectives

Detail the number of non-dominated solutions

###############################################################################
TOPSIS helper
  
  1) Vector normalization
  
  2) Re-establish Weights
  
  3) identify Ideal best/worst iteration by objective type
  
  4) Establish Distances & closeness

################################################################################
Apply TOPSIS to ecr output (res1)

Generate Pareto front (objectives) and identify corresponding decision vectors

matrix: rows = solutions, cols = f1..f4

list: each element is a decision vector x

Weights & benefit flags (adjust if you want different tradeoffs)

Assign the weights of each objective

Identify Best trade-off solution

(Optional) Identify the top-5 compromises

plot results:

Generate a 3-D Pareto plot of the algorithm run's ouptut that highlights the TOPSIS-selected optimum solution

################################################################################
export as csv

#Save the TOPSIS solution's decision variables, corresponding objective function evaluation, & datasets used in the Pareto plot generation


################################################################################

Post-Processing:

################################################################################

# Load in TOPSIS Solution Decision variables for x1-x11

Note: Nearly the entire function in Post-Processing is identical to the function in Processing with the exception of the TOPSIS decision variable implementation. Jump down to Post-Processing plotting section to avoid repetition.

Begin excecution of HydrOHS base function with TOPSIS output implemented

  ################################################################################
  ### Step 4 : Compute SCy from smoothed SC_rain

  Create sub-dataframe of just SC_rain
  
  Determine SCy interpolation type
  
  Remove any remaining na's in streamflow
  
  ################################################################################
  ### Step 5 : Apply Eckhardt RDF and calculate bf and qf
  
  Create dfl
  
  Define Recession Constant and BFImax values

  Create empty b vector
  
  fill in initial value of b

  iterate through remaining values
  
  assign b
  
  calculate y
  
  calculate bfi
  
  #Assigns 0 streamflow days a negative bfi for later removal
  
  round bfi to six digits (mitigates potential error in later peak identification)

  ################################################################################
  ### Step 6 : Tune SCy
  
  Omit na's present in ts and dfl via the warmup and cooldown periods after Eckhardt application
  
  Amplify, smooth and offset SC_rain
  
  Convert ts to xts object

  ################################################################################
  ### Step 7 : set SCy lag ON XTS DATA!!!
  
  Append SCy column
  
  Omit NAs in dfl

  ################################################################################
  ### Step 8 : Filter out non-perennial days & re-index
  
  Filter out non-perennial days
  
  Re-index (create artificial, truncated ts only for comparison of SC and BFI trends) & rename dataframe (dfl to dfl_filt)
  
  ################################################################################
  ### Step 9 : Get bfi peaks based on peak height (ph) and peak length (pl) adjustable variables
  
  Identify minpeakheight & minpeakdistance valyes (rounded to mitigate findpeak point difference errors)
  
  Identify bfi peaks
  
  Subset dfl at BFI peak indices
  
  ################################################################################
  ### Step 10 : Get SCb for peaks
  
  Populate SCb sub-dataframe with reference to dflp values
  
  Relabel columns in SCb
  
  Check for negative indices in SCb & set to 0 as needed

  Apply SCb to dflp as the SCb column

  
  ################################################################################
  ### Step 11 : Interpolate for entire SCb

  Using peaks identified in dflp/SCb for reference, interpolate SCb for the remaining rows

  Remove any remaining NA's 
  
  ################################################################################
  ### Step 12 : Chemical Mass Balance
  
  Chemical mass balance: Use the estimated SCb, b, SCy & y in conjunction with measured Q to calculate final SCq (modeled streamflow SC) estimations
  
  ################################################################################
  ### Step 13 : Extract calibration data and merge

  #Use finalized dataframe to generate a calibration dataframe (cali)

  ################################################################################
  ### Step 14 : Assign mass balance indicators

  ################################################################################
  ### Step 14A : Apply heteroscedasticity penalty 
  
  Compute residuals
  
  ################################################################################
  ### Step 15 : Objective functions

  Apply safeguard conditions

  Assign a High error penalty if safeguard conditions are violated in attempt to satisfy the four objective functions

  Establish objective functions:
 
  F1: RMSE between measured and modeled SC
    
  F2 &F3: Mass balance indicators (sign agreement)

  F4: BFImax value must correlate with maximum SCb value in dflp

End of HydrOHS base function

################################################################################
Plotting:

Note: All plots made available via ggplot & plotly for varying resolution needs

Plot 1: Time vs streamflow, baseflow, & quickflow (ft3/d)

Plot 2: Time vs SC, SCb, & SCy (µS/cm)

Plot 3: Time vs BFI

Plot 4: Measured SC vs Modeled SC Estimations

Plot 5: Time vs dflp BFI peaks

Plot 6: TIme vs dflp SC peaks





