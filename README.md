Disclaimer:
This fork was created to facilitate creation of a Docker image, and these images are created from forks of the source by myself (Jason Weirather), and I am NOT creator or author of the facets package.

Please cite:
Shen R, Seshan VE. FACETS: allele-specific copy number and clonal
heterogeneity analysis tool for high-throughput DNA sequencing. Nucleic Acids
Res. 2016 Sep 19;44(16):e131. doi: 10.1093/nar/gkw520. Epub 2016 Jun 7. PubMed
PMID: 27270079; PubMed Central PMCID: PMC5027494.

# facets
Algorithm to implement Fraction and Allele specific Copy number Estimate from Tumor/normal Sequencing.

**Travis CI** (_Linux & macOS_) [![Build Status](https://travis-ci.org/mskcc/facets.svg?branch=master)](https://travis-ci.org/mskcc/facets)  **Test coverage** [![codecov.io](https://codecov.io/github/mskcc/facets/coverage.svg?branch=master)](https://codecov.io/github/mskcc/facets?branch=master)

You can install the current version (along with the vignette) using the command

```R
devtools::install_github("mskcc/facets", build_vignettes = TRUE)
```

pctGCdata is a required package. So install that also (needs to be done only once)

```R
devtools::install_github("mskcc/pctGCdata")
```

## NOTES

### 2016_11_11 (version 0.5.6)

The new version estimates the log-ratio level corresponding to the diploid state. It is embedded into the procSample call.
In terms of using the package you can now do:

```R
rcmat <- readSnpMatrix(filename, ...)
xx <- preProcSample(rcmat, ...)
# specify cval you like
oo <- procSample(xx, cval = 300)
```

And go straight to
```R
emcncf(oo)
```
The ```emcncf2(oo)``` option imposes a clonal cluster structure. This function is currently being reworked. Please use with caution.

The output of procSample now has 4 elements:

* ```jointseg``` – same as before
* ```out``` – as before with 3 additional columns: cf, tcn, lcn
* ```dipLogR``` – the estimated location of diploid log-ratio value
* ```flags``` – this gives an indication of whether the dipLogR is estimated well.

If ```flags``` is NULL then no obvious problem with the dipLogR estimate. It can have two other comments: "mafR not sufficiently small" and "could be polyclonal 1 copy loss". The first one means that there aren't segments with sufficiently balanced alleles and so the estimate may not be great. The second one means that it looks like genome doubling; the only lower level segments are 2+1 and 2+0 (from 2+2); a model without genome doubling but single copy loss (1+0 from 1+1) with two different cellular fraction could fit better.

Note: I am not claiming that I have covered all possible scenarios that lead to bad dipLogR estimate. If you come across anything that doesn't seem right but flags is NULL let me know.

