# HyrdOHS - An Optimized Hydrograph Separation Algorithm For Baseflow- And Quickflow Estimation From High Resolution Streamflow And Specific Conductance Data
Conventional hydrograph separation methods such as recursive digital filters (RDFs) suffer from uncertainty due to subjectivity in the input parameter selection. This repository presents an Optimized Hydrograph Separation algorithm 
(HydrOHS) that tunes Eckhardt RDF baseflow estimations via chemical 
mass balance (CMB). Emphasis is placed on an objective estimation of 
RDF input parameters. 

The algorithm was developed in RStudio and is segmented into three distinct script files, Pre-Processing, Processing, & Post-Processing. 
Formatting of the initial streamflow, SC (specific conductance), and Precipitation SC is performed in Pre-Processing alonside the interpolation of missing streamflow & SC values and the recession constant analysis. The multi- objective optimization is performed in Processing, where the best optimum solution is subsequently selected via TOPSIS. The evaluation of the TOPSIS solution's performance is done in Post-Processing. Decision variables (x1-x11) corresponding to the TOPSIS solution are implemented into the base function of HydrOHS and the output is subsequentyl visualized via ggplot & plotly applications


Structure:

Pre-Processing

Processing

Post-Processing


Input Data Requirements:
HydrOHS requires the following three datasets:

  1) Streamflow Timeseries
     
  2) Measured Stream SC Timeseries
  
  3) Precipitation SC Timeseries
     
Note: Recession Constant Analysis required at least 2 years to generate the upper & lower bounds for the decision variable corresponding to the recession constant. At least 5 years per dataset are recommended to capture the full scope of potential recession constants for a given river system.
