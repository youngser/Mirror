# Discovering underlying dynamics in time series of networks

Avanti Athreya, Zachary Lubberts, Youngser Park, and Carey Priebe, "Discovering underlying dynamics in time series of networks", submitted, 2022.

https://arxiv.org/abs/2205.06877

## Abstract

Analyzing changes in network evolution is central to statistical network inference, as underscored by recent challenges of predicting and distinguishing pandemic-induced transformations in organizational and communication networks. We consider a joint network model in which each node has an associated time-varying low-dimensional latent vector of feature data, and connection probabilities are functions of these vectors. Under mild assumptions, the time-varying evolution of the latent vectors exhibits low-dimensional manifold structure under a suitable notion of distance. This distance can be approximated by a measure of separation between the observed networks themselves, and there exist Euclidean representations for underlying network structure, as characterized by this distance, at any given time. These Euclidean representations, called Euclidean mirrors, permit the visualization of network evolution and transform network inference questions such as change-point and anomaly detection into a classical setting. We illustrate our methodology with real and synthetic data, and identify change points corresponding to massive shifts in pandemic policies in a communication network of a large organization.

## MSFT TSG Data

We consider a time series of weighted communication networks, arising from the email communications between 32277 entities in a large organization, with one network being generated each month from January 2019 to December 2020, a period of 24 months. We obtain a clustering by applying Leiden clustering to the January 2019 network, obtaining 33 clusters that we retain throughout the two year period. We make use of this clustering to compute the Graph Encoder Embedding (GEE, which produces spectrally-derived estimates of invertible transformations of the original latent positions. For each time $t=1, \cdots, 24$, we obtain a matrix $\hat{Z}_t\in{R}^{32277\times 33}$, each row of which provides an estimate of these transformed latent positions. Constructing the distance matrix $D_{\hat{\psi}}=[\hat{d}_{MV}(\hat{Z}_t,\hat{Z}_{t'})]\in{R}^{24\times24}$, we apply CMDS to obtain the estimated curve $\hat{\psi}$, where the choice of dimension $c=2$ is based on the scree plot of $D_{\hat{\psi}}$. The nonlinear dimensionality reduction technique ISOMAP, which relies on a spectral decomposition of geodesic distances, can be applied to these points to extract an estimated 1-dimensional curve. This one-dimensional curve exhibits some changes from the previous trend in Spring 2020 and a much sharper qualitative transformation in July 2020. What is striking is that both these qualitative shifts correspond to policy changes: in Spring 2020, there was an initial shift in operations, widely regarded at the time as temporary. In mid-summer 2020, nearly the peak of the second wave of of COVID-19, it was much clearer that these organizational shifts were likely permanent, or at least significantly longer-lived.

<p align = "center">
<img src = "https://www.cis.jhu.edu/~parky/OrgSci/Umap-n2v-t16-19-proc14-1.png">
</p>
<p align = "center">
Fig 1. Evidence of structural network dissimilarity across time. Visualizations are of anonymized and aggregated Microsoft communications networks in April and July 2020, with two communities highlighted.
During this time period, our analysis demonstrates that
the network as a whole experiences a major structural shock coincident with the pandemic work-from-home order.
However, this shock is \emph{not} experienced by all network communities:
the green community highlighted here experiences just a constant drift,
while the blue community experiences a major shock.
(See Figure 2.3,
 wherein the overall network behavior,
 as well as the two highlighted communities with their different temporal behavior,
 are depicted.) The figures here are two-dimensional renderings of temporal snapshots of a large (n=32277) complex network;
hence, any conclusions based on this visualization alone are entirely notional.
</p>


## Synthetic Data

We use real data to obtain a distribution from which we may resample. Such a network bootstrap permits us to test our asymptotic results through replicable simulations that are grounded in actual data. To this end, we consider the true latent position distribution at each time to be equally likely to be any row of the GEE-obtained estimates from the real data, $\hat{Z}_t \in {R}^{32277 \times 33}$, for $t=1,\ldots,24.$ Given a sample size $n_s$, for each time, we sample these rows uniformly and with replacement to get a matrix of latent positions $X_t\in{R}^{n_s\times 33}$. We treat this matrix as the generating latent position matrix for independent adjacency matrices  $A_t\sim{RDPG}(X_t)$. Note that if for sample $i$, we choose row $j$ of $\hat{Z}_1$ at time $t=1$, then the same row $j$ of $\hat{Z}_t$ will be used for all times $t=1,\ldots,24$ for that sample, so that the original dependence structure is preserved. We may now apply the methods described in our theorems, namely ASE of the adjacency matrices followed by Procrustes alignment, to obtain the estimates $\hat{X}_t$, along with the associated distance estimates. 

1. take $32277 \times 33$ embeddings for each time,
2. given sample size $n_s$, sample rows (with replacement) for each time (use the same sampled rows for all time),
3. generate a tsg of RDPG's,
4. run ASE into d=33, do procrustes to align them,
5. then do our manifold learning procedure, i.e., isomap o cmds o D.

To reproduce the Figure 6 in the manuscript, we provide the code `mirror-synthetic.Rmd` file. The code is parallelized and may take ~15 minutes to run for each replicate of bootstrap data. The code will save the results in the `Output` folder, and the final plot will look like the attached figure.

NB: To rerun the code, make sure to change `rerun = FALSE` to `rerun = TRUE` in the code chunk in the `rmarkdown` file.




<p align = "center">
<img src = "https://www.cis.jhu.edu/~parky/IsoMirror/synthetic.png">
</p>
<p align = "center">
Fig 6. Pandemic effect recovered from synthetic data. For each of 100 replicates of bootstrapped data, with ns = 30000 for each replicate, we repeat procedure in bottom left panel of Figure 4. Sigmages plotted in a box-and-whisker plot. Pandemic effect in summer of 2020 is visible in all but a few replicates; effect in March-April is still identified in the majority of replicates.
</p>



```
# R version 4.3.2 (2023-10-31)
# Platform: aarch64-apple-darwin20 (64-bit)
# Running under: macOS Sonoma 14.2.1
# 
# Matrix products: default
# BLAS:   /Library/Frameworks/R.framework/Versions/4.3-arm64/Resources/lib/libRblas.0.dylib 
# LAPACK: /Library/Frameworks/R.framework/Versions/4.3-arm64/Resources/lib/libRlapack.dylib;  LAPACK version 3.11.0
# 
# locale:
# [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
# 
# time zone: Asia/Seoul
# tzcode source: internal
# 
# attached base packages:
# [1] parallel  stats     graphics  grDevices utils     datasets  methods  
# [8] base     
# 
# other attached packages:
#  [1] doParallel_1.0.17 iterators_1.0.14  foreach_1.5.2     forcats_1.0.0    
#  [5] stringr_1.5.1     dplyr_1.1.4       purrr_1.0.2       readr_2.1.4      
#  [9] tidyr_1.3.0       tibble_3.2.1      ggplot2_3.4.4     tidyverse_2.0.0  
# [13] vegan_2.6-4       lattice_0.22-5    permute_0.9-7     scales_1.3.0     
# [17] lubridate_1.9.3   irlba_2.3.5.1     Matrix_1.6-5      igraph_2.0.3     
# [21] pacman_0.5.1      knitr_1.45       
# 
# loaded via a namespace (and not attached):
#  [1] sass_0.4.8        utf8_1.2.4        generics_0.1.3    stringi_1.8.3    
#  [5] hms_1.1.3         digest_0.6.33     magrittr_2.0.3    evaluate_0.23    
#  [9] grid_4.3.2        timechange_0.2.0  bookdown_0.37     fastmap_1.1.1    
# [13] jsonlite_1.8.8    mgcv_1.9-0        fansi_1.0.6       codetools_0.2-19 
# [17] jquerylib_0.1.4   cli_3.6.2         rlang_1.1.3       munsell_0.5.0    
# [21] splines_4.3.2     withr_2.5.2       cachem_1.0.8      yaml_2.3.8       
# [25] tools_4.3.2       tzdb_0.4.0        colorspace_2.1-0  vctrs_0.6.5      
# [29] R6_2.5.1          lifecycle_1.0.4   MASS_7.3-60       cluster_2.1.6    
# [33] pkgconfig_2.0.3   bslib_0.6.1       pillar_1.9.0      gtable_0.3.4     
# [37] glue_1.7.0        rmdformats_1.0.4  xfun_0.41         tidyselect_1.2.0 
# [41] rstudioapi_0.15.0 htmltools_0.5.7   nlme_3.1-164      rmarkdown_2.25   
# [45] compiler_4.3.2
```
