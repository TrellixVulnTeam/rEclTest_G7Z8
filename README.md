
<!-- README.md is generated from README.Rmd. Please edit that file -->

# rEcl

The goal of `rEcl` is to serve as an R wrapper of the class
`EclBinaryParser`, written in Python by Konstantin Sermyagin, a
reservoir engineer. The class converts the reservoir simulation output
files generated by Eclipse from binary to dataframes.

## Installation

For the moment, `rEcl` is only available through Github.

Once is completed it will be submitted to CRAN. You can install it in
the meantime by two methods:

  - cloning or downloading the package from Github
  - install it using `devtools::install_github("f0nzie/rEcl", ref =
    "the_branch")`, where the branch could be `master` or `develop`,
    depending how newer or bleeding edge you like it.

## Requirements

  - R 3.5.3
  - Rtools 3.5
  - RStudio 1.2+. *I used RStudio preview 1.2.1327 for the development
    of the package.*
  - Python Anaconda3-2018.12-Windows-x86\_64
  - Conda environment 3.6 with pandas and numpy installed. *I called
    this environment* `pyres`

## Files used for testing

For testing `rEcl` and `EclBinaryParser` I used the output binary files
from the reservoir simulation of the Volve field.

    VOLVE_2016.INIT
    VOLVE_2016.RSSPEC
    VOLVE_2016.SMSPEC
    VOLVE_2016.UNSMRY

You can find a copy of these files in this repository under
[rEcl/inst/python/volve](https://github.com/f0nzie/rEcl/tree/master/inst/python/volve),
but the `rEcl` package will not install them. You will have to copy
these files manually. In the future, I plan to download the files
directly from Zenodo or Google drive; mainly, because these files are
too big for an R package.

## Functions for the class EclBinaryParser

  - `get_dimens`
  - `is_dual`
  - `get_actnum`
  - `get_seqnum_dates`
  - `read_prop_array`
  - `read_prop_time`
  - `read_vectors`

### New functions

  - `get_vectors_shape`: get the shape or dimensions of the vectors
    dataframe
  - `get_vector_names`: get the names of all the vectors
  - `get_vector_column`: get the values for a vector-column

## Examples

### Example reservoir model SPE6

We start by reading the file `SPE6_FRAC.UNSMRY`. This file , because is
relatively small, we can include it with the package. We willread it
from the package installation folder.

``` r
library(reticulate)

reticulate::use_condaenv("pyres", required = TRUE)
reticulate::py_config()
#> python:         /home/superuser/anaconda3/envs/pyres/bin/python
#> libpython:      /home/superuser/anaconda3/envs/pyres/lib/libpython3.6m.so
#> pythonhome:     /home/superuser/anaconda3/envs/pyres:/home/superuser/anaconda3/envs/pyres
#> version:        3.6.8 |Anaconda, Inc.| (default, Dec 30 2018, 01:22:34)  [GCC 7.3.0]
#> numpy:          /home/superuser/anaconda3/envs/pyres/lib/python3.6/site-packages/numpy
#> numpy_version:  1.16.2
#> 
#> NOTE: Python version was forced by RETICULATE_PYTHON
```

``` r
library(rEcl)

ecl_folder <- system.file("rawdata", package = "rEcl")
ecl_folder
#> [1] "/home/superuser/R/x86_64-pc-linux-gnu-library/3.5/rEcl/rawdata"
unsmry_file <- file.path(ecl_folder, "spe6", "SPE6_FRAC.UNSMRY")
file.exists(unsmry_file)
#> [1] TRUE
```

We connect to Python and load the class `EclBinaryParser` which resides
in the Python package called `restools`. You can take a look at
`restools` under the R installation folder in your lcoal disk.

Once we connect and load the Python package, we create an instance of
the class `EclBinaryParser` providing the parse object `py` and the full
name of the Eclipse binary file.

``` r
py <- restools_connect()
parser <- EclBinaryParser(py, unsmry_file)
```

First basic task is finding the dimensions of the reservoir model. We do
that with `get_dimensions`.

``` r
get_dimensions(parser)
#> DIMENS(ni=10, nj=1, nk=10)
```

This is a heavier operation; reading the vectors.

``` r
vectors <- read_vectors(parser)
```

Get the shape or dimensions of the vector dataframe.

``` r
get_vectors_shape(parser)
#> [1] 69 32
```

We get the names of the vectors we specified in our input file.

``` r
get_vector_names(parser)
#>  [1] "BGSAT" "BOSAT" "BPR"   "BRS"   "BWSAT" "FGOR"  "FGPR"  "FOPR" 
#>  [9] "FPR"   "TIME"  "WBHP"  "YEARS"
```

We now want a dataframe corresponding to a specific vector-column with
`get_vector_column`:

``` r
get_vector_column(parser, "FOPR")
#>           FOPR
#> 1    0.0000000
#> 2  495.8547363
#> 3  496.4673462
#> 4  497.7044983
#> 5  500.0000000
#> 6  500.0000000
#> 7  500.0000000
#> 8  500.0000000
#> 9  500.0000000
#> 10 500.0000000
#> 11 499.0129700
#> 12 495.0864258
#> 13 491.3752441
#> 14 487.6884460
#> 15 483.7983093
#> 16 479.4172058
#> 17 474.9851074
#> 18 470.4046021
#> 19 465.6196289
#> 20 460.7243347
#> 21 455.7453308
#> 22 450.6983032
#> 23 445.5942688
#> 24 440.4420471
#> 25 435.2494202
#> 26 430.0222778
#> 27 424.0520325
#> 28 416.5676270
#> 29 408.7698059
#> 30 400.7507019
#> 31 392.5427246
#> 32 384.1553650
#> 33 375.7556763
#> 34 367.2411499
#> 35 358.5085144
#> 36 349.5070496
#> 37 339.1749573
#> 38 327.4499817
#> 39 315.0457153
#> 40 301.9628906
#> 41 288.1910400
#> 42 273.9568787
#> 43 259.3403320
#> 44 243.6156769
#> 45 223.3641968
#> 46 201.8397217
#> 47 179.7216034
#> 48 157.3689117
#> 49 135.5422974
#> 50 114.8100815
#> 51  91.3848877
#> 52  66.5469284
#> 53  44.1556320
#> 54  26.0932941
#> 55  13.4400187
#> 56   5.8621721
#> 57   5.2417884
#> 58   4.5437417
#> 59   3.6096404
#> 60   2.4702997
#> 61   1.3012712
#> 62   0.4364305
#> 63   0.0000000
#> 64   0.0000000
#> 65   0.0000000
#> 66   0.0000000
#> 67   0.0000000
#> 68   0.0000000
#> 69   0.0000000
```

Finally, because the function `get_vector_column` is vectorized, we can
get a dataframe of multiple columns.

``` r
# get several vectors at once
df_vars <- get_vector_column(parser, c("FPR", "FGOR", "FOPR"))
df_vars
#>         FPR         FGOR        FOPR
#> 1  6025.137     0.000000   0.0000000
#> 2  6021.500     1.530000 495.8547363
#> 3  6010.570     1.530000 496.4673462
#> 4  5977.642     1.530000 497.7044983
#> 5  5878.311     1.530000 500.0000000
#> 6  5743.715     1.530000 500.0000000
#> 7  5609.000     1.530000 500.0000000
#> 8  5528.037     1.538324 500.0000000
#> 9  5492.169     1.562090 500.0000000
#> 10 5458.588     1.583304 500.0000000
#> 11 5426.020     1.608501 499.0129700
#> 12 5394.494     1.637577 495.0864258
#> 13 5365.298     1.668722 491.3752441
#> 14 5336.807     1.703061 487.6884460
#> 15 5307.525     1.744257 483.7983093
#> 16 5278.622     1.811011 479.4172058
#> 17 5248.898     1.894031 474.9851074
#> 18 5217.372     2.003969 470.4046021
#> 19 5185.498     2.126142 465.6196289
#> 20 5153.688     2.257681 460.7243347
#> 21 5121.876     2.397633 455.7453308
#> 22 5090.011     2.545463 450.6983032
#> 23 5058.050     2.700851 445.5942688
#> 24 5025.961     2.863592 440.4420471
#> 25 4993.717     3.033530 435.2494202
#> 26 4961.294     3.210622 430.0222778
#> 27 4927.479     3.415624 424.0520325
#> 28 4890.290     3.686419 416.5676270
#> 29 4852.142     3.985715 408.7698059
#> 30 4813.382     4.311690 400.7507019
#> 31 4773.960     4.664652 392.5427246
#> 32 4733.835     5.046370 384.1553650
#> 33 4693.025     5.447596 375.7556763
#> 34 4651.508     5.877616 367.2411499
#> 35 4609.229     6.347281 358.5085144
#> 36 4566.122     6.864860 349.5070496
#> 37 4518.661     7.555875 339.1749573
#> 38 4465.955     8.423036 327.4499817
#> 39 4411.505     9.426800 315.0457153
#> 40 4355.158    10.592511 301.9628906
#> 41 4296.767    11.953240 288.1910400
#> 42 4236.273    13.519985 273.9568787
#> 43 4173.643    15.325132 259.3403320
#> 44 4108.362    17.547583 243.6156769
#> 45 4034.673    20.967762 223.3641968
#> 46 3956.873    25.385218 201.8397217
#> 47 3875.409    31.051266 179.7216034
#> 48 3790.427    38.419727 157.3689117
#> 49 3702.283    47.972599 135.5422974
#> 50 3611.458    60.411133 114.8100815
#> 51 3515.281    81.203598  91.3848877
#> 52 3412.050   119.070526  66.5469284
#> 53 3306.926   189.455231  44.1556320
#> 54 3201.213   333.402679  26.0932941
#> 55 3096.375   662.164490  13.4400187
#> 56 2993.682  1529.078735   5.8621721
#> 57 2983.476  1709.756104   5.2417884
#> 58 2970.660  1971.567749   4.5437417
#> 59 2950.695  2479.002441   3.6096404
#> 60 2919.802  3612.904297   2.4702997
#> 61 2872.318  6819.601074   1.3012712
#> 62 2800.109 20102.908203   0.4364305
#> 63 2800.150     0.000000   0.0000000
#> 64 2800.146     0.000000   0.0000000
#> 65 2800.146     0.000000   0.0000000
#> 66 2800.146     0.000000   0.0000000
#> 67 2800.146     0.000000   0.0000000
#> 68 2800.146     0.000000   0.0000000
#> 69 2800.146     0.000000   0.0000000
```

## Example reservoir model PUNQ-S3

PUNQ-S3 is a synthetic reservoir model that is used for testing and
calibrating reservoir simulators. These are the files available with the
package:

    PUNQS3.INIT
    PUNQS3.INSPEC
    PUNQS3.RSSPEC
    PUNQS3.SMSPEC
    PUNQS3.UNRST
    PUNQS3.UNSMR

You may list the files with:

`list.files(system.file("python", "volve", package = "rEcl"))`

``` r
library(reticulate)

reticulate::use_condaenv("pyres", required = TRUE)
reticulate::py_config()
#> python:         /home/superuser/anaconda3/envs/pyres/bin/python
#> libpython:      /home/superuser/anaconda3/envs/pyres/lib/libpython3.6m.so
#> pythonhome:     /home/superuser/anaconda3/envs/pyres:/home/superuser/anaconda3/envs/pyres
#> version:        3.6.8 |Anaconda, Inc.| (default, Dec 30 2018, 01:22:34)  [GCC 7.3.0]
#> numpy:          /home/superuser/anaconda3/envs/pyres/lib/python3.6/site-packages/numpy
#> numpy_version:  1.16.2
#> 
#> NOTE: Python version was forced by RETICULATE_PYTHON
```

``` r
library(rEcl)

ecl_folder <- system.file("rawdata", package = "rEcl")
unsmry_file <- file.path(ecl_folder, "PUNQS3", "PUNQS3.UNSMRY")
file.exists(unsmry_file)
#> [1] TRUE
```

``` r
# connect to Python and start a class instance
py <- restools_connect()
parser <- EclBinaryParser(py, unsmry_file)
```

``` r
# dimensions of the reservoir model
get_dimensions(parser)
#> DIMENS(ni=19, nj=28, nk=5)
```

``` r
# reading the Eclipse vectors. this may take few seconds id the reservoir is too complex
vectors <- read_vectors(parser)

# Get the shape or dimensions of the vector dataframe.
get_vectors_shape(parser)
#> [1] 179  15
```

``` r
# We get the names of the vectors we specified in our input file.
get_vector_names(parser)
#>  [1] "FGOR"     "FGPT"     "FOPR"     "FOPT"     "FWCT"     "FWPT"    
#>  [7] "TIME"     "TIMESTEP" "WBHP"     "YEARS"
```

``` r
# dataframe corresponding to a specific vector-column
get_vector_column(parser, "FOPR")
#>          FOPR
#> 1      0.0000
#> 2      0.0000
#> 3    600.0000
#> 4    600.0000
#> 5    600.0000
#> 6    600.0000
#> 7    600.0000
#> 8    600.0000
#> 9    600.0000
#> 10   600.0000
#> 11   600.0000
#> 12  1200.0000
#> 13  1200.0000
#> 14  1200.0000
#> 15  1200.0000
#> 16  1200.0000
#> 17  1200.0000
#> 18   600.0000
#> 19   600.0000
#> 20   600.0000
#> 21   600.0000
#> 22   600.0000
#> 23   600.0000
#> 24   300.0000
#> 25   300.0000
#> 26   300.0000
#> 27   300.0000
#> 28   300.0000
#> 29   300.0000
#> 30     0.0000
#> 31     0.0000
#> 32     0.0000
#> 33     0.0000
#> 34     0.0000
#> 35     0.0000
#> 36     0.0000
#> 37     0.0000
#> 38     0.0000
#> 39   900.0000
#> 40   900.0000
#> 41   900.0000
#> 42   900.0000
#> 43   900.0000
#> 44   900.0000
#> 45   900.0000
#> 46   900.0000
#> 47     0.0000
#> 48     0.0000
#> 49     0.0000
#> 50     0.0000
#> 51   900.0000
#> 52   900.0000
#> 53   900.0000
#> 54   900.0000
#> 55   900.0000
#> 56   900.0000
#> 57   900.0000
#> 58   900.0000
#> 59     0.0000
#> 60     0.0000
#> 61     0.0000
#> 62     0.0000
#> 63   900.0000
#> 64   900.0000
#> 65   900.0000
#> 66   900.0000
#> 67   900.0000
#> 68   862.5000
#> 69   834.3750
#> 70   834.3750
#> 71     0.0000
#> 72     0.0000
#> 73     0.0000
#> 74     0.0000
#> 75   900.0000
#> 76   900.0000
#> 77   900.0000
#> 78   900.0000
#> 79   900.0000
#> 80   900.0000
#> 81   892.9762
#> 82     0.0000
#> 83     0.0000
#> 84     0.0000
#> 85     0.0000
#> 86   900.0000
#> 87   900.0000
#> 88   898.3598
#> 89   894.4315
#> 90   890.6831
#> 91   888.4182
#> 92   880.6496
#> 93     0.0000
#> 94     0.0000
#> 95     0.0000
#> 96     0.0000
#> 97   897.3006
#> 98   893.7467
#> 99   889.1471
#> 100  884.1654
#> 101  877.8188
#> 102  874.2231
#> 103  860.3490
#> 104    0.0000
#> 105    0.0000
#> 106    0.0000
#> 107    0.0000
#> 108  889.7090
#> 109  884.1975
#> 110  873.6402
#> 111  861.9726
#> 112  851.0178
#> 113  842.5769
#> 114  820.3649
#> 115    0.0000
#> 116    0.0000
#> 117    0.0000
#> 118    0.0000
#> 119  851.9354
#> 120  843.3365
#> 121  832.3793
#> 122  821.0728
#> 123  809.2431
#> 124  796.5966
#> 125  765.6595
#> 126    0.0000
#> 127    0.0000
#> 128    0.0000
#> 129    0.0000
#> 130  808.3836
#> 131  798.3890
#> 132  783.0869
#> 133  767.2960
#> 134  751.8171
#> 135  740.5112
#> 136  710.6010
#> 137    0.0000
#> 138    0.0000
#> 139    0.0000
#> 140    0.0000
#> 141  747.0986
#> 142  737.8694
#> 143  724.7645
#> 144  710.1848
#> 145  694.0875
#> 146  681.2035
#> 147  651.1420
#> 148    0.0000
#> 149    0.0000
#> 150    0.0000
#> 151    0.0000
#> 152  681.1065
#> 153  673.4787
#> 154  662.4407
#> 155  650.0897
#> 156  637.5364
#> 157  628.9332
#> 158  611.1095
#> 159    0.0000
#> 160    0.0000
#> 161    0.0000
#> 162    0.0000
#> 163  637.6225
#> 164  630.9486
#> 165  621.4207
#> 166  611.5229
#> 167  603.4231
#> 168  598.5114
#> 169  587.7632
#> 170    0.0000
#> 171    0.0000
#> 172    0.0000
#> 173    0.0000
#> 174  612.2325
#> 175  606.1571
#> 176  597.6083
#> 177  589.1541
#> 178  583.0701
#> 179  579.7202
```

``` r
# get a dataframe of multiple columns
# vectorized function to get several vectors at once
df_vars <- get_vector_column(parser, c("YEARS", "FGOR", "FOPR", "FWCT"))
df_vars
#>            YEARS     FGOR      FOPR         FWCT
#> 1   0.000000e+00  0.00000    0.0000 0.000000e+00
#> 2   2.737851e-05  0.00000    0.0000 0.000000e+00
#> 3   3.011636e-04 74.00000  600.0000 1.991938e-07
#> 4   1.122519e-03 74.00000  600.0000 6.092408e-07
#> 5   2.765229e-03 73.98709  600.0000 1.132328e-06
#> 6   7.693361e-03 73.86024  600.0000 1.893949e-06
#> 7   2.247776e-02 73.55646  600.0000 2.900575e-06
#> 8   5.367557e-02 73.21950  600.0000 3.938527e-06
#> 9   8.487337e-02 73.00764  600.0000 4.601429e-06
#> 10  1.615332e-01 72.68385  600.0000 5.630160e-06
#> 11  2.464066e-01 72.41563  600.0000 6.494209e-06
#> 12  2.491444e-01 72.15381 1200.0000 7.345203e-06
#> 13  2.573580e-01 71.76186 1200.0000 8.643978e-06
#> 14  2.819986e-01 71.25349 1200.0000 1.037846e-05
#> 15  3.285421e-01 70.75266 1200.0000 1.214476e-05
#> 16  4.134155e-01 70.17004 1200.0000 1.427279e-05
#> 17  4.955510e-01 69.71758 1200.0000 1.598290e-05
#> 18  4.982888e-01 69.71760  600.0000 1.494059e-05
#> 19  5.065024e-01 69.71758  600.0000 1.371125e-05
#> 20  5.311431e-01 69.71758  600.0000 1.269446e-05
#> 21  5.804244e-01 69.71757  600.0000 1.229585e-05
#> 22  6.652977e-01 69.71755  600.0000 1.241445e-05
#> 23  7.474332e-01 69.71754  600.0000 1.270893e-05
#> 24  7.501711e-01 69.71757  300.0000 1.215319e-05
#> 25  7.583846e-01 69.71756  300.0000 1.146253e-05
#> 26  7.830253e-01 69.71754  300.0000 1.078179e-05
#> 27  8.323066e-01 69.71754  300.0000 1.035980e-05
#> 28  9.144422e-01 69.71754  300.0000 1.017579e-05
#> 29  9.993156e-01 69.71753  300.0000 1.015527e-05
#> 30  1.002053e+00  0.00000    0.0000 0.000000e+00
#> 31  1.010267e+00  0.00000    0.0000 0.000000e+00
#> 32  1.034908e+00  0.00000    0.0000 0.000000e+00
#> 33  1.108830e+00  0.00000    0.0000 0.000000e+00
#> 34  1.330595e+00  0.00000    0.0000 0.000000e+00
#> 35  1.665982e+00  0.00000    0.0000 0.000000e+00
#> 36  2.001369e+00  0.00000    0.0000 0.000000e+00
#> 37  3.000684e+00  0.00000    0.0000 0.000000e+00
#> 38  4.000000e+00  0.00000    0.0000 0.000000e+00
#> 39  4.002738e+00 69.71751  900.0000 4.737859e-06
#> 40  4.010952e+00 69.71751  900.0000 7.063799e-06
#> 41  4.035592e+00 69.71752  900.0000 9.577207e-06
#> 42  4.109514e+00 69.71754  900.0000 1.231731e-05
#> 43  4.278152e+00 69.71426  900.0000 1.543161e-05
#> 44  4.495551e+00 69.37884  900.0000 1.844080e-05
#> 45  4.747433e+00 68.98219  900.0000 2.287366e-05
#> 46  4.999316e+00 70.45412  900.0000 2.878149e-05
#> 47  5.002053e+00  0.00000    0.0000 0.000000e+00
#> 48  5.010267e+00  0.00000    0.0000 0.000000e+00
#> 49  5.023956e+00  0.00000    0.0000 0.000000e+00
#> 50  5.037645e+00  0.00000    0.0000 0.000000e+00
#> 51  5.040383e+00 69.88659  900.0000 2.333518e-05
#> 52  5.048597e+00 70.27151  900.0000 2.540866e-05
#> 53  5.073237e+00 71.19053  900.0000 2.780255e-05
#> 54  5.147160e+00 73.33044  900.0000 3.096905e-05
#> 55  5.322382e+00 80.44121  900.0000 3.758295e-05
#> 56  5.497604e+00 87.36105  900.0000 4.494251e-05
#> 57  5.749486e+00 92.70155  900.0000 5.707620e-05
#> 58  6.001369e+00 96.02811  900.0000 7.095271e-05
#> 59  6.004107e+00  0.00000    0.0000 0.000000e+00
#> 60  6.012321e+00  0.00000    0.0000 0.000000e+00
#> 61  6.026010e+00  0.00000    0.0000 0.000000e+00
#> 62  6.039699e+00  0.00000    0.0000 0.000000e+00
#> 63  6.042437e+00 91.45794  900.0000 6.279253e-05
#> 64  6.050650e+00 92.18629  900.0000 6.570385e-05
#> 65  6.075291e+00 93.51507  900.0000 6.967890e-05
#> 66  6.149213e+00 95.59954  900.0000 7.567459e-05
#> 67  6.323066e+00 97.99612  900.0000 8.774507e-05
#> 68  6.496920e+00 92.81242  862.5000 9.963721e-05
#> 69  6.748802e+00 87.26064  834.3750 2.618798e-04
#> 70  7.000684e+00 85.83868  834.3750 4.863491e-04
#> 71  7.003422e+00  0.00000    0.0000 0.000000e+00
#> 72  7.011636e+00  0.00000    0.0000 0.000000e+00
#> 73  7.025325e+00  0.00000    0.0000 0.000000e+00
#> 74  7.039014e+00  0.00000    0.0000 0.000000e+00
#> 75  7.041752e+00 86.70294  900.0000 4.489492e-04
#> 76  7.049966e+00 87.83990  900.0000 4.583297e-04
#> 77  7.074606e+00 89.97019  900.0000 4.820266e-04
#> 78  7.148529e+00 92.37147  900.0000 5.483835e-04
#> 79  7.322382e+00 93.35823  900.0000 7.107569e-04
#> 80  7.496235e+00 93.45702  900.0000 8.821991e-04
#> 81  8.000000e+00 91.03961  892.9762 2.582569e-03
#> 82  8.002738e+00  0.00000    0.0000 0.000000e+00
#> 83  8.010951e+00  0.00000    0.0000 0.000000e+00
#> 84  8.024641e+00  0.00000    0.0000 0.000000e+00
#> 85  8.038330e+00  0.00000    0.0000 0.000000e+00
#> 86  8.041068e+00 85.23295  900.0000 2.548188e-03
#> 87  8.049281e+00 86.28516  900.0000 2.580381e-03
#> 88  8.073922e+00 87.84223  898.3598 2.679979e-03
#> 89  8.147844e+00 89.01424  894.4315 2.975781e-03
#> 90  8.321697e+00 88.99358  890.6831 3.652469e-03
#> 91  8.495551e+00 88.36535  888.4182 4.312418e-03
#> 92  8.999315e+00 85.94995  880.6496 6.275867e-03
#> 93  9.002053e+00  0.00000    0.0000 0.000000e+00
#> 94  9.010267e+00  0.00000    0.0000 0.000000e+00
#> 95  9.023956e+00  0.00000    0.0000 0.000000e+00
#> 96  9.037645e+00  0.00000    0.0000 0.000000e+00
#> 97  9.040383e+00 80.63602  897.3006 6.069751e-03
#> 98  9.048596e+00 81.49801  893.7467 6.148878e-03
#> 99  9.073237e+00 82.93967  889.1471 6.343846e-03
#> 100 9.147160e+00 84.22858  884.1654 6.848571e-03
#> 101 9.322382e+00 84.12869  877.8188 7.899145e-03
#> 102 9.497604e+00 83.41657  874.2231 8.837595e-03
#> 103 1.000137e+01 81.24713  860.3490 1.098902e-02
#> 104 1.000411e+01  0.00000    0.0000 0.000000e+00
#> 105 1.001232e+01  0.00000    0.0000 0.000000e+00
#> 106 1.002601e+01  0.00000    0.0000 0.000000e+00
#> 107 1.003970e+01  0.00000    0.0000 0.000000e+00
#> 108 1.004244e+01 76.28202  889.7090 1.051499e-02
#> 109 1.005065e+01 77.26228  884.1975 1.059827e-02
#> 110 1.007529e+01 78.67303  873.6402 1.080811e-02
#> 111 1.014921e+01 79.81296  861.9726 1.122721e-02
#> 112 1.032307e+01 79.90886  851.0178 1.202733e-02
#> 113 1.049692e+01 79.46638  842.5769 1.280397e-02
#> 114 1.100068e+01 77.76192  820.3649 1.501318e-02
#> 115 1.100342e+01  0.00000    0.0000 0.000000e+00
#> 116 1.101164e+01  0.00000    0.0000 0.000000e+00
#> 117 1.102532e+01  0.00000    0.0000 0.000000e+00
#> 118 1.103901e+01  0.00000    0.0000 0.000000e+00
#> 119 1.104175e+01 74.01922  851.9354 1.427874e-02
#> 120 1.104997e+01 74.25053  843.3365 1.446566e-02
#> 121 1.107461e+01 75.35580  832.3793 1.478798e-02
#> 122 1.114853e+01 76.53138  821.0728 1.540074e-02
#> 123 1.132238e+01 76.59998  809.2431 1.687727e-02
#> 124 1.149624e+01 76.16484  796.5966 1.851964e-02
#> 125 1.200000e+01 75.66885  765.6595 2.392858e-02
#> 126 1.200274e+01  0.00000    0.0000 0.000000e+00
#> 127 1.201095e+01  0.00000    0.0000 0.000000e+00
#> 128 1.202464e+01  0.00000    0.0000 0.000000e+00
#> 129 1.203833e+01  0.00000    0.0000 0.000000e+00
#> 130 1.204107e+01 73.34157  808.3836 2.283560e-02
#> 131 1.204928e+01 73.58937  798.3890 2.318827e-02
#> 132 1.207392e+01 74.12044  783.0869 2.391178e-02
#> 133 1.214784e+01 74.87678  767.2960 2.542312e-02
#> 134 1.232170e+01 75.25618  751.8171 2.851319e-02
#> 135 1.249555e+01 75.31657  740.5112 3.422970e-02
#> 136 1.299932e+01 75.54026  710.6010 5.062482e-02
#> 137 1.300205e+01  0.00000    0.0000 0.000000e+00
#> 138 1.301027e+01  0.00000    0.0000 0.000000e+00
#> 139 1.302396e+01  0.00000    0.0000 0.000000e+00
#> 140 1.303765e+01  0.00000    0.0000 0.000000e+00
#> 141 1.304038e+01 72.94955  747.0986 5.180321e-02
#> 142 1.304860e+01 73.27383  737.8694 5.236844e-02
#> 143 1.307324e+01 73.87908  724.7645 5.389619e-02
#> 144 1.314716e+01 74.68990  710.1848 5.790187e-02
#> 145 1.332238e+01 75.43992  694.0875 6.573480e-02
#> 146 1.349760e+01 75.81366  681.2035 7.270485e-02
#> 147 1.400137e+01 76.38380  651.1420 8.832157e-02
#> 148 1.400411e+01  0.00000    0.0000 0.000000e+00
#> 149 1.401232e+01  0.00000    0.0000 0.000000e+00
#> 150 1.402601e+01  0.00000    0.0000 0.000000e+00
#> 151 1.403970e+01  0.00000    0.0000 0.000000e+00
#> 152 1.404244e+01 73.59885  681.1065 9.002720e-02
#> 153 1.405065e+01 73.94077  673.4787 9.016947e-02
#> 154 1.407529e+01 74.58649  662.4407 9.088600e-02
#> 155 1.414921e+01 75.45151  650.0897 9.317731e-02
#> 156 1.432307e+01 76.21648  637.5364 9.829839e-02
#> 157 1.449692e+01 76.55766  628.9332 1.050718e-01
#> 158 1.500068e+01 76.94323  611.1095 1.222561e-01
#> 159 1.500342e+01  0.00000    0.0000 0.000000e+00
#> 160 1.501164e+01  0.00000    0.0000 0.000000e+00
#> 161 1.502532e+01  0.00000    0.0000 0.000000e+00
#> 162 1.503901e+01  0.00000    0.0000 0.000000e+00
#> 163 1.504175e+01 73.94164  637.6225 1.269501e-01
#> 164 1.504997e+01 74.31033  630.9486 1.262482e-01
#> 165 1.507461e+01 75.00164  621.4207 1.256994e-01
#> 166 1.514853e+01 75.90739  611.5229 1.268540e-01
#> 167 1.532238e+01 76.96578  603.4231 1.312406e-01
#> 168 1.549624e+01 77.58962  598.5114 1.356596e-01
#> 169 1.600000e+01 78.53827  587.7632 1.459805e-01
#> 170 1.600274e+01  0.00000    0.0000 0.000000e+00
#> 171 1.601095e+01  0.00000    0.0000 0.000000e+00
#> 172 1.602464e+01  0.00000    0.0000 0.000000e+00
#> 173 1.603833e+01  0.00000    0.0000 0.000000e+00
#> 174 1.604107e+01 75.18638  612.2325 1.512183e-01
#> 175 1.604928e+01 75.61112  606.1571 1.500371e-01
#> 176 1.607392e+01 76.41542  597.6083 1.486682e-01
#> 177 1.614784e+01 77.49184  589.1541 1.485046e-01
#> 178 1.632170e+01 78.48283  583.0701 1.517355e-01
#> 179 1.649555e+01 79.01240  579.7202 1.553172e-01
```

Eclipse vectors that are different in nature, such as well (start with
**W**) variables, will get a different size than field variables (start
with **F**):

``` r
# read a well variable
tibble::as_tibble(get_vector_column(parser, "WBHP"))
#> # A tibble: 1,074 x 1
#>     WBHP
#>    <dbl>
#>  1    0 
#>  2    0 
#>  3  221.
#>  4  221.
#>  5  220.
#>  6  219.
#>  7  218.
#>  8  217.
#>  9  216.
#> 10  215.
#> # … with 1,064 more rows
```
