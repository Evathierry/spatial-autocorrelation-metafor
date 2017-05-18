# spatial-autocorrelation-metafor

Dear all,


Does someone know how to take into account spatial autocorrelation in a meta-analysis with the metafor R package ?

I work on fish abundance data and associated diversity metrics at 35 stations located along several large French rivers (Rhône, Vienne, Loire, Meuse, Seine).
Some of the stations are closer than others : for example, 7 stations are located in the same area (distance <10 km) while some of them are located in different catchments without direct connectivity. Consequently, I expect that my data/results will be strongly spatially autocorrelated.

Initially
I am looking for a way to correct the time series meta-analysis for this spatial heterogeneity in R. Ideally, I was thinking of a method that would allow the weighting of the different time series in the meta-analysis according to their relative distance along the river network.


##########  What I've performed for the moment #############

TAB_AnnBio3 #Main table with the different species abundances and diversity metrics (~1000 lines and ~60 columns)
library(metafor)
library(Kendall)
library(boot)

## [OFF] Computation of regression coefficient using Mann-Kendall trend analyses (Hamed & Rao 1998)
ri_S # correlation coefficient of the 35 study sites (lines) and 60 species/metrics (columns)
VarSi # Variance extracted from the trend analysis (35 lines / 60 colunms)

## Example with the metric i = 43 (total abundance)
# Version 1 - without weighting the study sites
  i=43
  ri.final_MD1 = rma(yi = ri_S[,i], vi = VarSi[,i], measure = "GEN")

# Version 2 - Weighting the study sites according to proximity groups (the 35 study sites can be grouped according to 11 "areas") --> a first step into considering spatial links between study sites
  vec_W # vector of weigths (length = 35)
  i=43
  ri.final_MD2 = rma(yi = ri_S[,i], vi = VarSi[,i], measure = "GEN", weights = vec_W)



########## What I would like to do : take into account spatial autocorrelation with a distance matrix  #########

I have a distance matrix like this ; 

# #TAB_DIST_STA7 # Distance matrix in km ; 10000 = disconnected sites (different catchments)

#             STA1        STA2       STA3       STA4      STA5       STA6        STA7       STA8      STA9    
# STA1        0           6          10000      10000     10000      10000       10000      10000     10000  
# STA2        6           0          10000      10000     10000      10000       10000      10000     10000
# STA3        10000       10000      0          1         1          6           11         11        13  
# STA4        10000       10000      1          0         1          5           10         10        12  
       
 
I have though about the function "rma.mv" of the "metafor" package to try to account for spatial autocorrelation. 
I supposed that I should use the 'R' and 'random' arguments because these arguments can be used in a situation where there is a correlation induced by a phylogenetic history among organisms, which could be perhaps considered as a similar issue (except that it's not about a phylogenetic distance but a geographic distance).
For more on the rma.mv function, you can refer to this document https://cran.r-project.org/web/packages/metafor/metafor.pdf (p.207 see 'random' and 'R' arguments)

Maybe something like that ? 

i=43
ri.mv_MD3 = rma.mv(yi = ri_S[,i], V = VarSi[,i], random = ~ 1 | STA, data=ri_S, R = list (STA = TAB_DIST_STA7))


Can anyone help me ? 


Eva
