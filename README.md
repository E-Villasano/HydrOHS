# HyrdOHS - An Optimized Hydrograph Separation Algorithm For Baseflow- And Quickflow Estimation From High Resolution Streamflow And Specific Conductance Data
Conventional hydrograph separation methods such as recursive digital filters (RDFs) suffer from uncertainty due to subjectivity in the input parameter selection. This repository presents an Optimized Hydrograph Separation algorithm 
(HydrOHS) that tunes Eckhardt RDF baseflow estimations via chemical 
mass balance (CMB). Emphasis is placed on an objective estimation of 
RDF input parameters

HydrOHS uses multi objective optimization to tune the Eckhardt 
RDF with CMB:
𝑏𝑖= 1−β𝑎𝑏𝑖−1+(1−𝑎)β𝑦𝑖
1−𝑎β
Where 𝑎 is recession constant, β is Maximum Baseflow Index (𝐵𝐹𝐼𝑚𝑎𝑥), 
b is baseflow, y  is streamflow, and i  is timestep interval.
𝑆𝐶𝑦𝑖=𝑆𝐶𝑏𝑖𝑏𝑖+𝑆𝐶𝑞𝑖𝑞𝑖
𝑦𝑖
Where 𝑆𝐶𝑦 is streamflow SC, 𝑆𝐶𝑏 is baseflow SC, 𝑆𝐶𝑞 is quickflow SC, b  
is baseflow, q  is quickflow, y  is streamflow, and i  is timestep interval
