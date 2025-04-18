Lab 05 - La Quinta is Spanish for next to Denny’s, Pt. 2
================
Zi Li
Feb 12

### Load packages and data

``` r
# install.packages("devtools")
devtools::install_github("rstudio-education/dsbox")

library(tidyverse) 
library(dsbox) 
```

``` r
states <- read_csv("data/states.csv")
```

### Exercise 1

``` r
dennys_ak <- dennys %>%
  filter(state == "AK")
nrow(dennys_ak)
```

    ## [1] 3

``` r
laquinta_ak <- laquinta %>%
  filter(state == "AK")
nrow(laquinta_ak)
```

    ## [1] 2

``` r
# 3 dennys, 2 la_quinta.
```

### Exercise 2

``` r
# How many pairings are there between all Denny’s and all La Quinta locations in Alaska? 
# 2x3=6.

nrow(dennys_ak)
```

    ## [1] 3

``` r
nrow(laquinta_ak)
```

    ## [1] 2

``` r
num_pairing <- nrow(dennys_ak) * nrow(laquinta_ak)
print(num_pairing)
```

    ## [1] 6

### Exercise 3

``` r
# we will join the two data frames. 
dn_lq_ak <- full_join(dennys_ak, laquinta_ak, 
                      by = "state")
```

    ## Warning in full_join(dennys_ak, laquinta_ak, by = "state"): Detected an unexpected many-to-many relationship between `x` and `y`.
    ## ℹ Row 1 of `x` matches multiple rows in `y`.
    ## ℹ Row 1 of `y` matches multiple rows in `x`.
    ## ℹ If a many-to-many relationship is expected, set `relationship =
    ##   "many-to-many"` to silence this warning.

``` r
dn_lq_ak
```

    ## # A tibble: 6 × 11
    ##   address.x     city.x state zip.x longitude.x latitude.x address.y city.y zip.y
    ##   <chr>         <chr>  <chr> <chr>       <dbl>      <dbl> <chr>     <chr>  <chr>
    ## 1 2900 Denali   Ancho… AK    99503       -150.       61.2 3501 Min… "\nAn… 99503
    ## 2 2900 Denali   Ancho… AK    99503       -150.       61.2 4920 Dal… "\nFa… 99709
    ## 3 3850 Debarr … Ancho… AK    99508       -150.       61.2 3501 Min… "\nAn… 99503
    ## 4 3850 Debarr … Ancho… AK    99508       -150.       61.2 4920 Dal… "\nFa… 99709
    ## 5 1929 Airport… Fairb… AK    99701       -148.       64.8 3501 Min… "\nAn… 99503
    ## 6 1929 Airport… Fairb… AK    99701       -148.       64.8 4920 Dal… "\nFa… 99709
    ## # ℹ 2 more variables: longitude.y <dbl>, latitude.y <dbl>

…

### Exercise 4

``` r
# How many observations are in the joined dn_lq_ak data frame? 
# What are the names of the variables in this data frame.

# answer for Ex.4: we have 6 observations in the joined dn_lq_ak data frame; Column 3 is state, which is the same for both data frame. column 1 - 6 are address, city, state, zipcode,longitude and latitude respectively. And, the .x in the variable names means the variable comes from the x data frame / .y means the variable comes from the y data frame; which is our dennys and laquinta data frame and they're all represented same state -> ak.  
```

…

### Exercise 5

``` r
# What function from the tidyverse do we use the add a new variable to a data frame while keeping the existing variables?

haversine <- function(long1, lat1, long2, lat2, round = 3) {
  # convert to radians
  long1 <- long1 * pi / 180
  lat1 <- lat1 * pi / 180
  long2 <- long2 * pi / 180
  lat2 <- lat2 * pi / 180

  R <- 6371 # Earth mean radius in km

  a <- sin((lat2 - lat1) / 2)^2 + cos(lat1) * cos(lat2) * sin((long2 - long1) / 2)^2
  d <- R * 2 * asin(sqrt(a))

  return(round(d, round)) # distance in km
}
```

…

### Exercise 6

``` r
# Calculate the distances between all pairs of Denny’s and La Quinta locations and save this variable as distance. 

dn_lq_ak <- dn_lq_ak %>%
   mutate(.data=dn_lq_ak, distance = haversine (longitude.x, latitude.x, longitude.y, latitude.y, round = 3))
```

… \### Exercise 7

``` r
# Calculate the minimum distance between a Denny’s and La Quinta for each Denny’s location. 

dn_lq_ak_mindist <- dn_lq_ak %>%
  group_by(address.x) %>%
  summarize(closest = min(distance))
```

### Exercise 8

``` r
# Describe the distribution of the distances Denny’s and the nearest La Quinta locations in Alaska. Also include an appropriate visualization and relevant summary statistics.

hist(dn_lq_ak_mindist$closest, 
     main="Distribution of Closest Denny's to La Quinta in AK", 
     xlab="Distance (km)", 
     col="pink", 
     border="black")
```

![](lab-05_files/figure-gfm/Ex8-1.png)<!-- -->

``` r
summary(dn_lq_ak_mindist$closest)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   2.035   3.616   5.197   4.410   5.598   5.998

``` r
print(dn_lq_ak_mindist$closest)
```

    ## [1] 5.197 2.035 5.998

``` r
# the data points are: Min: 2.035; Median: 5.197, and Max: 5.998
```

### Exercise 9

``` r
dennys_nc <- dennys %>%
  filter(state == "NC")
nrow(dennys_nc)
```

    ## [1] 28

``` r
laquinta_nc <- laquinta %>%
  filter(state == "NC")
nrow(laquinta_nc)
```

    ## [1] 12

``` r
num_pairing <- nrow(dennys_nc) * nrow(laquinta_nc)
print(num_pairing)
```

    ## [1] 336

``` r
dn_lq_nc <- full_join(dennys_nc, laquinta_nc, 
                      by = "state")
```

    ## Warning in full_join(dennys_nc, laquinta_nc, by = "state"): Detected an unexpected many-to-many relationship between `x` and `y`.
    ## ℹ Row 1 of `x` matches multiple rows in `y`.
    ## ℹ Row 1 of `y` matches multiple rows in `x`.
    ## ℹ If a many-to-many relationship is expected, set `relationship =
    ##   "many-to-many"` to silence this warning.

``` r
dn_lq_nc
```

    ## # A tibble: 336 × 11
    ##    address.x    city.x state zip.x longitude.x latitude.x address.y city.y zip.y
    ##    <chr>        <chr>  <chr> <chr>       <dbl>      <dbl> <chr>     <chr>  <chr>
    ##  1 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 165 Hwy … "\nBo… 28607
    ##  2 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 3127 Slo… "\nCh… 28208
    ##  3 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 4900 Sou… "\nCh… 28217
    ##  4 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 4414 Dur… "\nDu… 27707
    ##  5 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 1910 Wes… "\nDu… 27713
    ##  6 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 1201 Lan… "\nGr… 27407
    ##  7 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 1607 Fai… "\nCo… 28613
    ##  8 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 191 Cres… "\nCa… 27518
    ##  9 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 2211 Sum… "\nRa… 27612
    ## 10 1 Regent Pa… Ashev… NC    28806       -82.6       35.6 1001 Aer… "\nMo… 27560
    ## # ℹ 326 more rows
    ## # ℹ 2 more variables: longitude.y <dbl>, latitude.y <dbl>

``` r
haversine <- function(long1, lat1, long2, lat2, round = 3) {
  long1 <- long1 * pi / 180
  lat1 <- lat1 * pi / 180
  long2 <- long2 * pi / 180
  lat2 <- lat2 * pi / 180

  R <- 6371 

  a <- sin((lat2 - lat1) / 2)^2 + cos(lat1) * cos(lat2) * sin((long2 - long1) / 2)^2
  d <- R * 2 * asin(sqrt(a))
  return(round(d, round)) 
}

dn_lq_nc <- dn_lq_nc %>%
   mutate(.data=dn_lq_nc, distance = haversine (longitude.x, latitude.x, longitude.y, latitude.y, round = 3))

dn_lq_nc_mindist <- dn_lq_nc %>%
  group_by(address.x) %>%
  summarize(closest = min(distance))

hist(dn_lq_nc_mindist$closest, 
     main="Distribution of Closest Denny's to La Quinta in NC", 
     xlab="Distance (km)", 
     col="lightblue", 
     border="black")
```

![](lab-05_files/figure-gfm/Repeat%20the%20same%20analysis%20for%20NC-1.png)<!-- -->

``` r
summary(dn_lq_nc_mindist$closest)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   1.779  22.388  53.456  65.444  93.985 187.935

``` r
print(dn_lq_nc_mindist$closest)
```

    ##  [1] 107.801 120.422  26.711  36.130 187.935  39.115  70.144  59.544 114.947
    ## [10]  55.909 180.395  51.004 165.402  65.863   5.044  38.482  11.890   9.685
    ## [19]   1.779  49.675  78.825  89.380   5.064  81.444  23.497 118.326  19.062
    ## [28]  18.966

### Exercise 10

``` r
dennys_tx <- dennys %>%
  filter(state == "TX")
nrow(dennys_tx)
```

    ## [1] 200

``` r
laquinta_tx <- laquinta %>%
  filter(state == "TX")
nrow(laquinta_tx)
```

    ## [1] 237

``` r
num_pairing <- nrow(dennys_tx) * nrow(laquinta_tx)
print(num_pairing)
```

    ## [1] 47400

``` r
dn_lq_tx <- full_join(dennys_tx, laquinta_tx, 
                      by = "state")
```

    ## Warning in full_join(dennys_tx, laquinta_tx, by = "state"): Detected an unexpected many-to-many relationship between `x` and `y`.
    ## ℹ Row 1 of `x` matches multiple rows in `y`.
    ## ℹ Row 1 of `y` matches multiple rows in `x`.
    ## ℹ If a many-to-many relationship is expected, set `relationship =
    ##   "many-to-many"` to silence this warning.

``` r
dn_lq_tx
```

    ## # A tibble: 47,400 × 11
    ##    address.x    city.x state zip.x longitude.x latitude.x address.y city.y zip.y
    ##    <chr>        <chr>  <chr> <chr>       <dbl>      <dbl> <chr>     <chr>  <chr>
    ##  1 120 East I-… Abile… TX    79601       -99.6       32.4 3018 Cat… "\nAb… 79606
    ##  2 120 East I-… Abile… TX    79601       -99.6       32.4 3501 Wes… "\nAb… 79601
    ##  3 120 East I-… Abile… TX    79601       -99.6       32.4 14925 La… "\nAd… 75254
    ##  4 120 East I-… Abile… TX    79601       -99.6       32.4 909 East… "\nAl… 78516
    ##  5 120 East I-… Abile… TX    79601       -99.6       32.4 2400 Eas… "\nAl… 78332
    ##  6 120 East I-… Abile… TX    79601       -99.6       32.4 1220 Nor… "\nAl… 75013
    ##  7 120 East I-… Abile… TX    79601       -99.6       32.4 1165 Hwy… "\nAl… 76009
    ##  8 120 East I-… Abile… TX    79601       -99.6       32.4 880 Sout… "\nAl… 77511
    ##  9 120 East I-… Abile… TX    79601       -99.6       32.4 1708 Int… "\nAm… 79103
    ## 10 120 East I-… Abile… TX    79601       -99.6       32.4 9305 Eas… "\nAm… 79118
    ## # ℹ 47,390 more rows
    ## # ℹ 2 more variables: longitude.y <dbl>, latitude.y <dbl>

``` r
haversine <- function(long1, lat1, long2, lat2, round = 3) {
  long1 <- long1 * pi / 180
  lat1 <- lat1 * pi / 180
  long2 <- long2 * pi / 180
  lat2 <- lat2 * pi / 180

  R <- 6371 

  a <- sin((lat2 - lat1) / 2)^2 + cos(lat1) * cos(lat2) * sin((long2 - long1) / 2)^2
  d <- R * 2 * asin(sqrt(a))
  return(round(d, round)) 
}

dn_lq_tx <- dn_lq_tx %>%
   mutate(.data=dn_lq_tx, distance = haversine (longitude.x, latitude.x, longitude.y, latitude.y, round = 3))

dn_lq_tx_mindist <- dn_lq_tx %>%
  group_by(address.x) %>%
  summarize(closest = min(distance))

hist(dn_lq_tx_mindist$closest, 
     main="Distribution of Closest Denny's to La Quinta in TX", 
     xlab="Distance (km)", 
     col="lightgreen", 
     border="black")
```

![](lab-05_files/figure-gfm/Repeat%20the%20same%20analysis%20for%20Texas-1.png)<!-- -->

``` r
summary(dn_lq_tx_mindist$closest)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##  0.0160  0.7305  3.3715  5.7918  6.6303 60.5820

``` r
print(dn_lq_tx_mindist$closest)
```

    ##   [1] 33.619  1.391 33.878 10.272 14.026  1.735  1.097 37.599  0.618  6.881
    ##  [11] 43.449  5.299  4.289  3.897  0.057  0.042  1.291  0.157  5.769  3.961
    ##  [21]  7.849  0.233  1.380  1.803 24.370  1.964  9.411 19.867  6.601  0.316
    ##  [31]  5.943  0.528  1.406  0.131  3.971 13.051  0.655 11.710  0.318  0.320
    ##  [41]  5.074  1.184  0.561  0.026  3.894 10.188  5.900  4.153  2.812  0.922
    ##  [51] 15.429 10.038  6.312 10.533  0.879  3.981  0.066 35.923  0.600  3.492
    ##  [61]  0.184  7.796  4.377  5.511  1.062  3.037  3.882  5.265 14.750  2.791
    ##  [71]  1.557  1.692  3.395  4.320  2.680 14.652  2.412  3.348  9.558  0.282
    ##  [81]  7.878  6.474  0.151  8.044  2.718  4.607  4.509  0.126  2.576 38.885
    ##  [91]  0.908  4.569  0.634  2.703  0.037 14.119  5.902  7.123  0.056  5.180
    ## [101] 17.684  0.320  0.239  2.411  1.036  0.387  8.443  0.646  5.704  1.991
    ## [111]  0.027  1.317 15.748  3.798  0.536  3.133  5.206  8.021  2.742  0.062
    ## [121]  2.341  4.467  0.387 14.087 14.618  0.087  5.283  1.820  4.072  2.198
    ## [131]  0.887  2.125 13.311 16.836  3.312 51.755  6.718  2.678  1.665  7.307
    ## [141]  4.962  0.085  0.699  0.618  0.016  0.086  1.326  7.810  3.659  0.073
    ## [151]  3.777  9.623  1.430  0.229  3.518  1.250  1.748  2.493  1.115  0.288
    ## [161]  3.563  7.893  6.488  7.685  0.796  0.336  5.616  0.066  0.128  0.336
    ## [171]  3.881  4.289  0.118  0.034  7.989  6.996  5.855  0.290  0.741  1.187
    ## [181]  8.625  0.219  2.784  5.940  5.179  0.143  3.582  5.503  0.268  1.485
    ## [191] 11.479  0.638  7.472  5.869  1.078 10.003  5.855  5.698 18.590 60.582

### Exercise 11

``` r
# I choose IL, cause I love Chicago. 

dennys_il <- dennys %>%
  filter(state == "IL")
nrow(dennys_il)
```

    ## [1] 56

``` r
laquinta_il <- laquinta %>%
  filter(state == "IL")
nrow(laquinta_il)
```

    ## [1] 17

``` r
num_pairing <- nrow(dennys_il) * nrow(laquinta_il)
print(num_pairing)
```

    ## [1] 952

``` r
dn_lq_il <- full_join(dennys_il, laquinta_il, 
                      by = "state")
```

    ## Warning in full_join(dennys_il, laquinta_il, by = "state"): Detected an unexpected many-to-many relationship between `x` and `y`.
    ## ℹ Row 1 of `x` matches multiple rows in `y`.
    ## ℹ Row 1 of `y` matches multiple rows in `x`.
    ## ℹ If a many-to-many relationship is expected, set `relationship =
    ##   "many-to-many"` to silence this warning.

``` r
dn_lq_il
```

    ## # A tibble: 952 × 11
    ##    address.x    city.x state zip.x longitude.x latitude.x address.y city.y zip.y
    ##    <chr>        <chr>  <chr> <chr>       <dbl>      <dbl> <chr>     <chr>  <chr>
    ##  1 140 Racehor… Alort… IL    62205       -90.1       38.6 1900 Cen… "\nCh… 61820
    ##  2 140 Racehor… Alort… IL    62205       -90.1       38.6 One Sout… "\nCh… 60606
    ##  3 140 Racehor… Alort… IL    62205       -90.1       38.6 5688 Nor… "\nGu… 60031
    ##  4 140 Racehor… Alort… IL    62205       -90.1       38.6 4900A S.… "\nCh… 60615
    ##  5 140 Racehor… Alort… IL    62205       -90.1       38.6 2000 Sou… "\nBa… 60015
    ##  6 140 Racehor… Alort… IL    62205       -90.1       38.6 1900 Eas… "\nEl… 60007
    ##  7 140 Racehor… Alort… IL    62205       -90.1       38.6 1 South … "\nOa… 60181
    ##  8 140 Racehor… Alort… IL    62205       -90.1       38.6 7255 Wes… "\nTi… 60477
    ##  9 140 Racehor… Alort… IL    62205       -90.1       38.6 855 79th… "\nWi… 60527
    ## 10 140 Racehor… Alort… IL    62205       -90.1       38.6 6 Gatewa… "\nCo… 62234
    ## # ℹ 942 more rows
    ## # ℹ 2 more variables: longitude.y <dbl>, latitude.y <dbl>

``` r
haversine <- function(long1, lat1, long2, lat2, round = 3) {
  long1 <- long1 * pi / 180
  lat1 <- lat1 * pi / 180
  long2 <- long2 * pi / 180
  lat2 <- lat2 * pi / 180

  R <- 6371 

  a <- sin((lat2 - lat1) / 2)^2 + cos(lat1) * cos(lat2) * sin((long2 - long1) / 2)^2
  d <- R * 2 * asin(sqrt(a))
  return(round(d, round)) 
}

dn_lq_il <- dn_lq_il %>%
   mutate(.data=dn_lq_il, distance = haversine (longitude.x, latitude.x, longitude.y, latitude.y, round = 3))

dn_lq_il_mindist <- dn_lq_il %>%
  group_by(address.x) %>%
  summarize(closest = min(distance))

hist(dn_lq_il_mindist$closest, 
     main="Distribution of Closest Denny's to La Quinta in IL", 
     xlab="Distance (km)", 
     col="orange", 
     border="black")
```

![](lab-05_files/figure-gfm/Repeat%20the%20same%20analysis%20for%20a%20state%20of%20my%20choosing-1.png)<!-- -->

``` r
summary(dn_lq_il_mindist$closest)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   0.149   3.113  12.405  23.212  31.954 113.267

``` r
print(dn_lq_il_mindist$closest)
```

    ##  [1]   8.296  13.887  38.801  10.565  94.186  10.443  31.267  44.262   0.569
    ## [10]   1.856  41.175  20.044  13.721  59.001  25.041  77.135   0.938   3.168
    ## [19]   1.134  67.679   4.816 113.267  29.148   4.887  11.181  25.867   5.911
    ## [28]   0.870  10.718   2.948   2.736  11.893  42.651   5.178  29.191   0.149
    ## [37]  29.581  34.725  14.103  90.808  10.136   0.647   1.598  70.567   0.927
    ## [46]  14.115   1.079   0.177  11.445   0.324  71.797  13.485   7.138  15.704
    ## [55]  34.016  12.916

### Exercise 12

``` r
# to know where this joke is most likely true, we need to analyze and compare the minimum distances between Denny’s and La Quinta locations across states.

# when I check my output: AK: Min: 2.035 km; Median: 5.197 km; Mean:4.41 km
# NC: Min: 1.779 km; Median: ~53.46 km; Mean: 65.44 km
# TX: Min: 0.016 km (very close!); Median: 3.37 km; Mean: 5.79 km
# IL: Min: 0.149 km; Median: 12.40 km; Mean: 23.21 km

min_distance_ak <- min(dn_lq_ak_mindist$closest, na.rm = TRUE)
median_distance_ak <- median(dn_lq_ak_mindist$closest, na.rm = TRUE)
mean_distance_ak <- mean(dn_lq_ak_mindist$closest, na.rm = TRUE)

cat("Alaska - Min Distance:", min_distance_ak, "km\n")
```

    ## Alaska - Min Distance: 2.035 km

``` r
cat("Alaska - Median Distance:", median_distance_ak, "km\n")
```

    ## Alaska - Median Distance: 5.197 km

``` r
cat("Alaska - Mean Distance:", mean_distance_ak, "km\n")
```

    ## Alaska - Mean Distance: 4.41 km

``` r
# repeat for NC.
min_distance_nc <- min(dn_lq_nc_mindist$closest, na.rm = TRUE)
median_distance_nc <- median(dn_lq_nc_mindist$closest, na.rm = TRUE)
mean_distance_nc <- mean(dn_lq_nc_mindist$closest, na.rm = TRUE)

cat("NC - Min Distance:", min_distance_nc, "km\n")
```

    ## NC - Min Distance: 1.779 km

``` r
cat("NC - Median Distance:", median_distance_nc, "km\n")
```

    ## NC - Median Distance: 53.4565 km

``` r
cat("NC - Mean Distance:", mean_distance_nc, "km\n")
```

    ## NC - Mean Distance: 65.44432 km

``` r
# repeat for TX.
min_distance_tx <- min(dn_lq_tx_mindist$closest, na.rm = TRUE)
median_distance_tx <- median(dn_lq_tx_mindist$closest, na.rm = TRUE)
mean_distance_tx <- mean(dn_lq_tx_mindist$closest, na.rm = TRUE)

cat("TX - Min Distance:", min_distance_tx, "km\n")
```

    ## TX - Min Distance: 0.016 km

``` r
cat("TX - Median Distance:", median_distance_tx, "km\n")
```

    ## TX - Median Distance: 3.3715 km

``` r
cat("TX - Mean Distance:", mean_distance_tx, "km\n")
```

    ## TX - Mean Distance: 5.7918 km

``` r
# repeat for IL.
min_distance_il <- min(dn_lq_il_mindist$closest, na.rm = TRUE)
median_distance_il <- median(dn_lq_il_mindist$closest, na.rm = TRUE)
mean_distance_il <- mean(dn_lq_il_mindist$closest, na.rm = TRUE)

cat("IL - Min Distance:", min_distance_il, "km\n")
```

    ## IL - Min Distance: 0.149 km

``` r
cat("IL - Median Distance:", median_distance_il, "km\n")
```

    ## IL - Median Distance: 12.4045 km

``` r
cat("IL - Mean Distance:", mean_distance_il, "km\n")
```

    ## IL - Mean Distance: 23.21191 km

``` r
# based on my output, TX is the winner! 
```
