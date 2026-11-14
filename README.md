# Wine Quality
Destiny McKenzie

# Introduction

The goals of this Deliverable:

1.  Get more familiar with R Markdown & GitHub

2.  Learn about EDA in R

3.  Generate Data Visualizations for EDA

4.  Perform EDA using R

For this Capstone Deliverable, extract some “actionable” insights from
the UCI Wine Quality Data Set. A wine producer has hired you to better
understand what factors affect wine quality. Hence, for this
deliverable, follow the Data Science process discussed in class.

# Business Understanding

**What makes good quality wine?**

Wine quality depends on a multitude of factors that are interconnected,
ranging from the fruit used to the activity of the yeast during the
fermentation process. Understanding the factors will give a clear
picture of the science and the intricacy behind what makes good wine.

Wine quality begins in the vineyard with terroir which is the unique
combination of soil, climate, and altitude that shapes a grape’s
chemical makeup. Terroir relies on the sugar to alcohol conversion,
where yeast transforms fruit sugars into ethanol at a rate of about
0.461 grams of alcohol per gram of sugar. Tannins are extracted from
grape skins, seeds, and oak barrels and creates a dry, puckering
sensation in young red wines, which drives the aging potential because
the tannin molecules bond together over time into smoother chains, while
white wines rely on acidity and sugar to age gracefully.

The Vinho Verde datasets capture only part of this picture. They include
measurements related to acidity (fixed acidity, volatile acidity, citric
acid, pH), sweetness and fermentation (residual sugar, alcohol,
density), and preservation (free and total sulfur dioxide, sulphates,
chlorides), along with a sensory quality score. They do not include
grape variety, vineyard conditions, yeast strain, or tannin levels, so
factors like terroir and tannin structure cannot be tested directly.
This analysis therefore focuses on which measurable chemical properties
are associated with higher quality ratings, and the findings should be
read as associations, not as a complete recipe for good wine. Red and
white wines differ fundamentally because red wines are fermented with
their grape skins and seeds at warmer temperatures (20–30°C) to extract
deep color, rich flavors, and drying tannins, whereas white wines are
pressed immediately and fermented juice-only at cooler temperatures
(15–18°C) to preserve crisp acidity and delicate floral aromas.
Consequently, red wines rely on tannins for structure, body, and
long-term aging, while white wines depend on high acidity for their
refreshing bite and preservation.

# Data Understanding

The datasets that are being used are redwine.csv and whitewine.csv,
related to red and white wine variants of the Portuguese “Vinho Verde”
wine. There are a total of 6497 total observations across both datasets.
Only physicochemical and sensory variables are available meaning that
there is not data about grape types, wine brand, and wine selling price.
Both sets contain the same 12 variables with no missing values. The sets
come from website: https://archive.ics.uci.edu/dataset/186/wine+quality

Based on the datasets these are columns in the table:

- fixed acidity

- volatile acidity

- citric acid

- residual sugar

- chlorides

- free sulfur dioxide

- total sulfur dioxide

- density

- pH

- sulphates

- alcohol

  Output variable (based on sensory data):

- quality (score between 0 and 10)

## Loading Data and Creating Description Table

``` r
# load the datasets
red_wine <- read.csv("redwine.csv")
white_wine <-read.csv("whitewine.csv")
```

``` r
# description table
library(tibble)  
```

    Warning: package 'tibble' was built under R version 4.5.3

``` r
missing_val <- colSums(is.na(red_wine))
colSums(is.na(white_wine))
```

           fixed.acidity     volatile.acidity          citric.acid 
                       0                    0                    0 
          residual.sugar            chlorides  free.sulfur.dioxide 
                       0                    0                    0 
    total.sulfur.dioxide              density                   pH 
                       0                    0                    0 
               sulphates              alcohol              quality 
                       0                    0                    0 

``` r
data <- tibble(
column_name = names(red_wine),
  type = c("Ratio", "Ratio", "Ratio", "Ratio","Ratio","Ratio","Ratio", 
           "Ratio","Interval","Ratio","Ratio", "Ordinal"),
  description = c(
  "Acids that do not evaporate easily (primarily tartaric acid). Higher values generally contribute to tartness and stability.",
  "Volatile acids, mainly acetic acid. High levels can create a vinegar-like taste and are usually undesirable.",
  "Amount of citric acid present. Can add freshness and flavor complexity to the wine.",
  "Sugar remaining after fermentation has finished. Higher values generally make wine taste sweeter.",
  "Salt content in the wine. Excessive levels may negatively affect taste.",
  "Portion of sulfur dioxide available to prevent microbial growth and oxidation.",
  "Total amount of sulfur dioxide (free + bound forms). Used as a preservative.",
  "Density of the wine (g/cm³). Influenced by alcohol and sugar content.",
  "Measure of acidity/basicity. Lower pH indicates a more acidic wine.",
  "Potassium sulfate and related compounds. Can contribute to sulfur dioxide levels and may improve wine stability and quality.",
  "Alcohol percentage by volume (% ABV).",
  "Wine quality score assigned by sensory experts, ranging from 0 to 10. This is the target variable."
),
  role     = c("feature", "feature", "feature", "feature", "feature", "feature", 
               "feature", "feature", "feature", "feature", "feature", "target"),
  units       = c(NA, NA, NA, NA, NA, NA, NA,
  "g/cm³",
  "pH scale",
  NA, "percent",
  "quality score (0-10)"), 

  missing     = missing_val
)

data
```

    # A tibble: 12 × 6
       column_name          type     description                 role  units missing
       <chr>                <chr>    <chr>                       <chr> <chr>   <dbl>
     1 fixed.acidity        Ratio    Acids that do not evaporat… feat… <NA>        0
     2 volatile.acidity     Ratio    Volatile acids, mainly ace… feat… <NA>        0
     3 citric.acid          Ratio    Amount of citric acid pres… feat… <NA>        0
     4 residual.sugar       Ratio    Sugar remaining after ferm… feat… <NA>        0
     5 chlorides            Ratio    Salt content in the wine. … feat… <NA>        0
     6 free.sulfur.dioxide  Ratio    Portion of sulfur dioxide … feat… <NA>        0
     7 total.sulfur.dioxide Ratio    Total amount of sulfur dio… feat… <NA>        0
     8 density              Ratio    Density of the wine (g/cm³… feat… g/cm³       0
     9 pH                   Interval Measure of acidity/basicit… feat… pH s…       0
    10 sulphates            Ratio    Potassium sulfate and rela… feat… <NA>        0
    11 alcohol              Ratio    Alcohol percentage by volu… feat… perc…       0
    12 quality              Ordinal  Wine quality score assigne… targ… qual…       0

# Data Preparation

The data can be accessed through the previously loaded datasets red_wine
and white_wine, which were used to create the data description table.
The datasets contain no missing values, so no additional pre-processing
is required prior to model development. Therefore, the data are
considered ready for analysis. Because the data are separated into two
datasets, one for red wine and one for white wine, a two-stage analysis
will be conducted to investigate the factors associated with wine
quality. Stage 1 will focus on the red_wine dataset, and Stage 2 will
focus on the white_wine dataset.

# Modeling + Evaluating

We begin by evaluating the distribution of key variables in the
datasets. This analysis will help determine the characteristics of
variables and findings for the research question. Correlation between
variables is also an important feature to be analyzed, so the
relationship between variables can be determined.

## Exploratory Data Analysis

### Stage 1 - Red Wine

``` r
# looking at the trends for red wine
summary(red_wine)
```

     fixed.acidity   volatile.acidity  citric.acid    residual.sugar  
     Min.   : 4.60   Min.   :0.1200   Min.   :0.000   Min.   : 0.900  
     1st Qu.: 7.10   1st Qu.:0.3900   1st Qu.:0.090   1st Qu.: 1.900  
     Median : 7.90   Median :0.5200   Median :0.260   Median : 2.200  
     Mean   : 8.32   Mean   :0.5278   Mean   :0.271   Mean   : 2.539  
     3rd Qu.: 9.20   3rd Qu.:0.6400   3rd Qu.:0.420   3rd Qu.: 2.600  
     Max.   :15.90   Max.   :1.5800   Max.   :1.000   Max.   :15.500  
       chlorides       free.sulfur.dioxide total.sulfur.dioxide    density      
     Min.   :0.01200   Min.   : 1.00       Min.   :  6.00       Min.   :0.9901  
     1st Qu.:0.07000   1st Qu.: 7.00       1st Qu.: 22.00       1st Qu.:0.9956  
     Median :0.07900   Median :14.00       Median : 38.00       Median :0.9968  
     Mean   :0.08747   Mean   :15.87       Mean   : 46.47       Mean   :0.9967  
     3rd Qu.:0.09000   3rd Qu.:21.00       3rd Qu.: 62.00       3rd Qu.:0.9978  
     Max.   :0.61100   Max.   :72.00       Max.   :289.00       Max.   :1.0037  
           pH          sulphates         alcohol         quality     
     Min.   :2.740   Min.   :0.3300   Min.   : 8.40   Min.   :3.000  
     1st Qu.:3.210   1st Qu.:0.5500   1st Qu.: 9.50   1st Qu.:5.000  
     Median :3.310   Median :0.6200   Median :10.20   Median :6.000  
     Mean   :3.311   Mean   :0.6581   Mean   :10.42   Mean   :5.636  
     3rd Qu.:3.400   3rd Qu.:0.7300   3rd Qu.:11.10   3rd Qu.:6.000  
     Max.   :4.010   Max.   :2.0000   Max.   :14.90   Max.   :8.000  

``` r
# creating histograms to see visuals of the trends
par(mfrow = c(2,3))
hist(red_wine$fixed.acidity)
hist(red_wine$volatile.acidity)
hist(red_wine$citric.acid)
hist(red_wine$residual.sugar)
hist(red_wine$chlorides)
hist(red_wine$free.sulfur.dioxide)
```

![](README_files/figure-commonmark/unnamed-chunk-3-1.png)

``` r
hist(red_wine$total.sulfur.dioxide)
hist(red_wine$density)
hist(red_wine$pH)
hist(red_wine$sulphates)
hist(red_wine$alcohol)
hist(red_wine$quality)
```

![](README_files/figure-commonmark/unnamed-chunk-3-2.png)

**Distribution of Red Wine** The red wine dataset contains several
chemical characteristics, including fixed acidity, volatile acidity,
citric acid, residual sugar, chlorides, sulfur dioxide, density, pH,
sulphates, and alcohol, along with a quality rating. The summary
statistics suggest that the distributions vary across the different
variables. Several variables, including residual sugar, chlorides, free
sulfur dioxide, total sulfur dioxide, and sulphates, appear to be
right-skewed, as there are long right tails present in the histogram and
the means are greater than their medians. In contrast, density and pH
have means and medians that are nearly the same, suggesting relatively
symmetric distributions.

There is also considerable variation in the ranges of some variables.
For instance, total sulfur dioxide ranges from 6 to 289, while residual
sugar ranges from 0.9 to 15.5. These large ranges, along with the
differences between their means and medians, suggest the possibility of
unusually high observations.

### Stage 2 - White Wine

``` r
# looking at the trends for white wine
summary(white_wine)
```

     fixed.acidity    volatile.acidity  citric.acid     residual.sugar  
     Min.   : 3.800   Min.   :0.0800   Min.   :0.0000   Min.   : 0.600  
     1st Qu.: 6.300   1st Qu.:0.2100   1st Qu.:0.2700   1st Qu.: 1.700  
     Median : 6.800   Median :0.2600   Median :0.3200   Median : 5.200  
     Mean   : 6.855   Mean   :0.2782   Mean   :0.3342   Mean   : 6.391  
     3rd Qu.: 7.300   3rd Qu.:0.3200   3rd Qu.:0.3900   3rd Qu.: 9.900  
     Max.   :14.200   Max.   :1.1000   Max.   :1.6600   Max.   :65.800  
       chlorides       free.sulfur.dioxide total.sulfur.dioxide    density      
     Min.   :0.00900   Min.   :  2.00      Min.   :  9.0        Min.   :0.9871  
     1st Qu.:0.03600   1st Qu.: 23.00      1st Qu.:108.0        1st Qu.:0.9917  
     Median :0.04300   Median : 34.00      Median :134.0        Median :0.9937  
     Mean   :0.04577   Mean   : 35.31      Mean   :138.4        Mean   :0.9940  
     3rd Qu.:0.05000   3rd Qu.: 46.00      3rd Qu.:167.0        3rd Qu.:0.9961  
     Max.   :0.34600   Max.   :289.00      Max.   :440.0        Max.   :1.0390  
           pH          sulphates         alcohol         quality     
     Min.   :2.720   Min.   :0.2200   Min.   : 8.00   Min.   :3.000  
     1st Qu.:3.090   1st Qu.:0.4100   1st Qu.: 9.50   1st Qu.:5.000  
     Median :3.180   Median :0.4700   Median :10.40   Median :6.000  
     Mean   :3.188   Mean   :0.4898   Mean   :10.51   Mean   :5.878  
     3rd Qu.:3.280   3rd Qu.:0.5500   3rd Qu.:11.40   3rd Qu.:6.000  
     Max.   :3.820   Max.   :1.0800   Max.   :14.20   Max.   :9.000  

``` r
# creating histograms to see visuals of the trends
par(mfrow = c(2,3))
hist(white_wine$fixed.acidity)
hist(white_wine$volatile.acidity)
hist(white_wine$citric.acid)
hist(white_wine$residual.sugar)
hist(white_wine$chlorides)
hist(white_wine$free.sulfur.dioxide)
```

![](README_files/figure-commonmark/unnamed-chunk-4-1.png)

``` r
hist(white_wine$total.sulfur.dioxide)
hist(white_wine$density)
hist(white_wine$pH)
hist(white_wine$sulphates)
hist(white_wine$alcohol)
hist(white_wine$quality)
```

![](README_files/figure-commonmark/unnamed-chunk-4-2.png)

**Distribution of White Wine** The white wine dataset displays a variety
of distribution shapes across its chemical characteristics. Fixed
acidity, density, and pH appear approximately symmetric, as their means
and medians are relatively close. Several other variables, including
residual sugar, volatile acidity, citric acid, chlorides, and sulphates,
show indications of right skewness, with their means exceeding their
medians and the histograms have a long right tail. Residual sugar
displays a particularly noticeable difference between its mean and
median, suggesting a more pronounced right-skewed distribution.

## Correlation Analysis

### Stage 1 - Red Wine

``` r
# correlation matrix
cor(red_wine)
```

                         fixed.acidity volatile.acidity citric.acid residual.sugar
    fixed.acidity           1.00000000     -0.256130895  0.67170343    0.114776724
    volatile.acidity       -0.25613089      1.000000000 -0.55249568    0.001917882
    citric.acid             0.67170343     -0.552495685  1.00000000    0.143577162
    residual.sugar          0.11477672      0.001917882  0.14357716    1.000000000
    chlorides               0.09370519      0.061297772  0.20382291    0.055609535
    free.sulfur.dioxide    -0.15379419     -0.010503827 -0.06097813    0.187048995
    total.sulfur.dioxide   -0.11318144      0.076470005  0.03553302    0.203027882
    density                 0.66804729      0.022026232  0.36494718    0.355283371
    pH                     -0.68297819      0.234937294 -0.54190414   -0.085652422
    sulphates               0.18300566     -0.260986685  0.31277004    0.005527121
    alcohol                -0.06166827     -0.202288027  0.10990325    0.042075437
    quality                 0.12405165     -0.390557780  0.22637251    0.013731637
                            chlorides free.sulfur.dioxide total.sulfur.dioxide
    fixed.acidity         0.093705186        -0.153794193          -0.11318144
    volatile.acidity      0.061297772        -0.010503827           0.07647000
    citric.acid           0.203822914        -0.060978129           0.03553302
    residual.sugar        0.055609535         0.187048995           0.20302788
    chlorides             1.000000000         0.005562147           0.04740047
    free.sulfur.dioxide   0.005562147         1.000000000           0.66766645
    total.sulfur.dioxide  0.047400468         0.667666450           1.00000000
    density               0.200632327        -0.021945831           0.07126948
    pH                   -0.265026131         0.070377499          -0.06649456
    sulphates             0.371260481         0.051657572           0.04294684
    alcohol              -0.221140545        -0.069408354          -0.20565394
    quality              -0.128906560        -0.050656057          -0.18510029
                             density          pH    sulphates     alcohol
    fixed.acidity         0.66804729 -0.68297819  0.183005664 -0.06166827
    volatile.acidity      0.02202623  0.23493729 -0.260986685 -0.20228803
    citric.acid           0.36494718 -0.54190414  0.312770044  0.10990325
    residual.sugar        0.35528337 -0.08565242  0.005527121  0.04207544
    chlorides             0.20063233 -0.26502613  0.371260481 -0.22114054
    free.sulfur.dioxide  -0.02194583  0.07037750  0.051657572 -0.06940835
    total.sulfur.dioxide  0.07126948 -0.06649456  0.042946836 -0.20565394
    density               1.00000000 -0.34169933  0.148506412 -0.49617977
    pH                   -0.34169933  1.00000000 -0.196647602  0.20563251
    sulphates             0.14850641 -0.19664760  1.000000000  0.09359475
    alcohol              -0.49617977  0.20563251  0.093594750  1.00000000
    quality              -0.17491923 -0.05773139  0.251397079  0.47616632
                             quality
    fixed.acidity         0.12405165
    volatile.acidity     -0.39055778
    citric.acid           0.22637251
    residual.sugar        0.01373164
    chlorides            -0.12890656
    free.sulfur.dioxide  -0.05065606
    total.sulfur.dioxide -0.18510029
    density              -0.17491923
    pH                   -0.05773139
    sulphates             0.25139708
    alcohol               0.47616632
    quality               1.00000000

The correlation matrix shows the relationships between wine quality, the
target variable, and the other explanatory variables in the dataset. The
purpose of this analysis is to identify which chemical characteristics
have the strongest relationships with wine quality. Based on the
correlation matrix, alcohol (0.48) has the strongest positive
relationship with quality, followed by sulphates (0.25) and citric acid
(0.23). This suggests that wines with higher alcohol content tend to
receive higher quality ratings. Citric acid also has a positive
relationship with quality, which may be related to its contribution to
the freshness and flavor profile of wine.

On the other hand, volatile acidity (-0.39) has the strongest negative
relationship with quality. Higher volatile acidity is associated with
lower quality ratings, most likely because elevated levels can
contribute undesirable vinegar flavors. Alcohol and volatile acidity
appear to have the strongest linear relationships with wine quality
among the variables examined.

**Scatterplots**

``` r
# alcohol
plot(red_wine$alcohol, red_wine$quality, main = "Alcohol vs Wine Quality", 
     xlab = "Alcohol", ylab = "Quality")

abline(lm(quality~ alcohol, data = red_wine), col = "red")
```

![](README_files/figure-commonmark/unnamed-chunk-6-1.png)

``` r
# volatile
plot(red_wine$volatile.acidity, red_wine$quality, 
     main = "volatile.acidity vs Wine Quality", xlab = "volatile.acidity", ylab = "Quality")

abline(lm(quality~ volatile.acidity, data = red_wine), col = "red")
```

![](README_files/figure-commonmark/unnamed-chunk-6-2.png)

``` r
# sulphates
plot(red_wine$sulphates, red_wine$quality, 
     main = "sulphates vs Wine Quality", xlab = "sulphates", ylab = "Quality")

abline(lm(quality~ sulphates, data = red_wine), col = "red")
```

![](README_files/figure-commonmark/unnamed-chunk-6-3.png)

``` r
# citric acid
plot(red_wine$citric.acid, red_wine$quality, 
     main = "citric.acid vs Wine Quality", xlab = "citric.acid", ylab = "Quality")

abline(lm(quality~ citric.acid, data = red_wine), col = "red")
```

![](README_files/figure-commonmark/unnamed-chunk-6-4.png)

The scatterplots show how each of the four variables most correlated
with quality relates to the rating. Because quality is a whole-number
score, the points fall in horizontal bands, so the red trend lines are
more informative than the individual points. Alcohol shows the clearest
upward trend (r = 0.48), higher-alcohol wines tend to receive higher
ratings, though there is spread at every alcohol level. Volatile acidity
is the only one of the four with a downward trend (r = -0.39), meaning
wines with more volatile acidity tend to be rated lower, which is
consistent with the vinegar-like taste associated with acetic acid.
Sulphates (r = 0.25) and citric acid (r = 0.23) both slope upward, but
the relationships are weak and the points are widely scattered around
the line. Sulphates also has a few very high values that may be pulling
the trend line. So no single variable explains quality on its own, which
suggests that a multivariable model will be needed to understand what
drives it.

### Stage 2 - White Wine

``` r
cor(white_wine)
```

                         fixed.acidity volatile.acidity  citric.acid residual.sugar
    fixed.acidity           1.00000000      -0.02269729  0.289180698     0.08902070
    volatile.acidity       -0.02269729       1.00000000 -0.149471811     0.06428606
    citric.acid             0.28918070      -0.14947181  1.000000000     0.09421162
    residual.sugar          0.08902070       0.06428606  0.094211624     1.00000000
    chlorides               0.02308564       0.07051157  0.114364448     0.08868454
    free.sulfur.dioxide    -0.04939586      -0.09701194  0.094077221     0.29909835
    total.sulfur.dioxide    0.09106976       0.08926050  0.121130798     0.40143931
    density                 0.26533101       0.02711385  0.149502571     0.83896645
    pH                     -0.42585829      -0.03191537 -0.163748211    -0.19413345
    sulphates              -0.01714299      -0.03572815  0.062330940    -0.02666437
    alcohol                -0.12088112       0.06771794 -0.075728730    -0.45063122
    quality                -0.11366283      -0.19472297 -0.009209091    -0.09757683
                           chlorides free.sulfur.dioxide total.sulfur.dioxide
    fixed.acidity         0.02308564       -0.0493958591          0.091069756
    volatile.acidity      0.07051157       -0.0970119393          0.089260504
    citric.acid           0.11436445        0.0940772210          0.121130798
    residual.sugar        0.08868454        0.2990983537          0.401439311
    chlorides             1.00000000        0.1013923521          0.198910300
    free.sulfur.dioxide   0.10139235        1.0000000000          0.615500965
    total.sulfur.dioxide  0.19891030        0.6155009650          1.000000000
    density               0.25721132        0.2942104109          0.529881324
    pH                   -0.09043946       -0.0006177961          0.002320972
    sulphates             0.01676288        0.0592172458          0.134562367
    alcohol              -0.36018871       -0.2501039415         -0.448892102
    quality              -0.20993441        0.0081580671         -0.174737218
                             density            pH   sulphates     alcohol
    fixed.acidity         0.26533101 -0.4258582910 -0.01714299 -0.12088112
    volatile.acidity      0.02711385 -0.0319153683 -0.03572815  0.06771794
    citric.acid           0.14950257 -0.1637482114  0.06233094 -0.07572873
    residual.sugar        0.83896645 -0.1941334540 -0.02666437 -0.45063122
    chlorides             0.25721132 -0.0904394560  0.01676288 -0.36018871
    free.sulfur.dioxide   0.29421041 -0.0006177961  0.05921725 -0.25010394
    total.sulfur.dioxide  0.52988132  0.0023209718  0.13456237 -0.44889210
    density               1.00000000 -0.0935914935  0.07449315 -0.78013762
    pH                   -0.09359149  1.0000000000  0.15595150  0.12143210
    sulphates             0.07449315  0.1559514973  1.00000000 -0.01743277
    alcohol              -0.78013762  0.1214320987 -0.01743277  1.00000000
    quality              -0.30712331  0.0994272457  0.05367788  0.43557472
                              quality
    fixed.acidity        -0.113662831
    volatile.acidity     -0.194722969
    citric.acid          -0.009209091
    residual.sugar       -0.097576829
    chlorides            -0.209934411
    free.sulfur.dioxide   0.008158067
    total.sulfur.dioxide -0.174737218
    density              -0.307123313
    pH                    0.099427246
    sulphates             0.053677877
    alcohol               0.435574715
    quality               1.000000000

The correlation matrix for white wine shows that alcohol has the
strongest positive linear association with quality (r = 0.43). Wines
with higher alcohol content tend to receive higher ratings, although
alcohol alone does not determine quality. Density (r = -0.307) has the
strongest negative association, followed by chlorides (r = -0.209),
volatile acidity (r = -0.19), and total sulfur dioxide (r = -0.174),
while fixed acidity (r = -0.11) is only weakly related. Density is
strongly negatively correlated with alcohol, so its relationship with
quality likely reflects alcohol content rather than an independent
effect. Higher chlorides (salt) and volatile acidity (vinegar-like) may
be perceived as off-flavors, and higher total sulfur dioxide may
indicate heavier preservation, which could affect aroma. These
associations are all modest, and since correlation does not imply
causation, a multivariable model is needed to separate their effects.

``` r
# alcohol
plot(white_wine$alcohol, white_wine$quality)
abline(lm(quality~alcohol, data = white_wine), col = "blue")
```

![](README_files/figure-commonmark/unnamed-chunk-8-1.png)

``` r
# density
plot(white_wine$density, white_wine$quality)
abline(lm(quality~density, data = white_wine), col = "blue")
```

![](README_files/figure-commonmark/unnamed-chunk-8-2.png)

``` r
# chlorides
plot(white_wine$chlorides, white_wine$quality)
abline(lm(quality~chlorides, data = white_wine), col = "blue")
```

![](README_files/figure-commonmark/unnamed-chunk-8-3.png)

``` r
# volatile acidity
plot(white_wine$volatile.acidity, white_wine$quality)
abline(lm(quality~volatile.acidity, data = white_wine), col = "blue")
```

![](README_files/figure-commonmark/unnamed-chunk-8-4.png)

``` r
# total sulfur dioxide
plot(white_wine$total.sulfur.dioxide, white_wine$quality)
abline(lm(quality~total.sulfur.dioxide, data = white_wine), col = "blue")
```

![](README_files/figure-commonmark/unnamed-chunk-8-5.png)

The scatterplots illustrate the correlations between quality and the
five predictors with the strongest associations. Because quality is
scored on a whole-number scale, the points form horizontal bands, so the
trend lines are more informative than individual points. Alcohol is the
only predictor with an upward trend (r = 0.43) higher-alcohol wines tend
to receive higher ratings. The other predictors slope downward. Density
(r = -0.31) shows the steepest decline, followed by chlorides (r =
-0.21), volatile acidity (r = -0.19), and total sulfur dioxide (r =
-0.17). Higher volatile acidity is associated with lower ratings,
consistent with the vinegar-like taste of acetic acid. Fixed acidity (r
= -0.11) has only a slight downward trend.

## Stating and Testing Hypotheses

Based on the EDA and background on wine chemistry, I hypothesize that
alcohol is positively associated with quality, and volatile acidity
negatively associated, in both red and white wine. Linear regression
assumptions (linearity, constant variance, normality of residuals,
independence, and low multicollinearity) will be checked first. If they
are reasonably met, predictors are compared using p-values and
standardized coefficients.

### Stage 1 - Red Wine

``` r
# loading package
library(car)
```

    Warning: package 'car' was built under R version 4.5.3

    Loading required package: carData

    Warning: package 'carData' was built under R version 4.5.3

``` r
# forming model
mod <- lm(quality ~ ., data = red_wine)
summary(mod)
```


    Call:
    lm(formula = quality ~ ., data = red_wine)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -2.68911 -0.36652 -0.04699  0.45202  2.02498 

    Coefficients:
                           Estimate Std. Error t value Pr(>|t|)    
    (Intercept)           2.197e+01  2.119e+01   1.036   0.3002    
    fixed.acidity         2.499e-02  2.595e-02   0.963   0.3357    
    volatile.acidity     -1.084e+00  1.211e-01  -8.948  < 2e-16 ***
    citric.acid          -1.826e-01  1.472e-01  -1.240   0.2150    
    residual.sugar        1.633e-02  1.500e-02   1.089   0.2765    
    chlorides            -1.874e+00  4.193e-01  -4.470 8.37e-06 ***
    free.sulfur.dioxide   4.361e-03  2.171e-03   2.009   0.0447 *  
    total.sulfur.dioxide -3.265e-03  7.287e-04  -4.480 8.00e-06 ***
    density              -1.788e+01  2.163e+01  -0.827   0.4086    
    pH                   -4.137e-01  1.916e-01  -2.159   0.0310 *  
    sulphates             9.163e-01  1.143e-01   8.014 2.13e-15 ***
    alcohol               2.762e-01  2.648e-02  10.429  < 2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.648 on 1587 degrees of freedom
    Multiple R-squared:  0.3606,    Adjusted R-squared:  0.3561 
    F-statistic: 81.35 on 11 and 1587 DF,  p-value: < 2.2e-16

``` r
vif(mod)
```

           fixed.acidity     volatile.acidity          citric.acid 
                7.767512             1.789390             3.128022 
          residual.sugar            chlorides  free.sulfur.dioxide 
                1.702588             1.481932             1.963019 
    total.sulfur.dioxide              density                   pH 
                2.186813             6.343760             3.329732 
               sulphates              alcohol 
                1.429434             3.031160 

``` r
# checking for assumptions
par(mfrow = c(1,1))
plot(mod)
```

![](README_files/figure-commonmark/unnamed-chunk-9-1.png)

![](README_files/figure-commonmark/unnamed-chunk-9-2.png)

![](README_files/figure-commonmark/unnamed-chunk-9-3.png)

![](README_files/figure-commonmark/unnamed-chunk-9-4.png)

The residuals vs fitted plot shows a nearly flat trend line at zero,
with a slight lift at the lower end of the fitted values. Linearity
assumption is reasonable. The scale-location plot shows a fairly
constant band of points, meaning the spread is roughly even. There is
still a dip in the trend line that increases as the fitted values
increase, but constant variance assumption is reasonable. Normality can
be assumed as the points follow the trend line in the Q-Q residuals plot
where only the lower tail end of the observations drift from the line.
There are also no concerning influential points based on the residuals
and leverage plot.

When looking at multicollinearity which is if two or more predictor
variables are strongly correlated with each other. Fixed acidity and
density are the highest at 7.8 and 6.3 respectively raising some
concerns with those predictors. Therefore, I will remove one predictor
at a time and examine if that improves multicollinearity, starting with
the highest fixed acidity.

**Removing Fixed Acidity From Model**

``` r
library(car)

# forming model
mod <- lm(quality ~ . - fixed.acidity, data = red_wine)
summary(mod)
```


    Call:
    lm(formula = quality ~ . - fixed.acidity, data = red_wine)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -2.65461 -0.36856 -0.04552  0.45670  2.03464 

    Coefficients:
                           Estimate Std. Error t value Pr(>|t|)    
    (Intercept)           6.1795700 13.4367180   0.460   0.6456    
    volatile.acidity     -1.0777894  0.1209486  -8.911  < 2e-16 ***
    citric.acid          -0.1353226  0.1387582  -0.975   0.3296    
    residual.sugar        0.0101047  0.0135372   0.746   0.4555    
    chlorides            -1.9684566  0.4076978  -4.828 1.51e-06 ***
    free.sulfur.dioxide   0.0045916  0.0021580   2.128   0.0335 *  
    total.sulfur.dioxide -0.0034272  0.0007089  -4.835 1.46e-06 ***
    density              -1.5167406 13.3889717  -0.113   0.9098    
    pH                   -0.5462340  0.1332577  -4.099 4.36e-05 ***
    sulphates             0.8995900  0.1130053   7.961 3.23e-15 ***
    alcohol               0.2900579  0.0222316  13.047  < 2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.648 on 1588 degrees of freedom
    Multiple R-squared:  0.3602,    Adjusted R-squared:  0.3561 
    F-statistic: 89.39 on 10 and 1588 DF,  p-value: < 2.2e-16

``` r
vif(mod)
```

        volatile.acidity          citric.acid       residual.sugar 
                1.784963             2.780557             1.386375 
               chlorides  free.sulfur.dioxide total.sulfur.dioxide 
                1.401232             1.939209             2.069396 
                 density                   pH            sulphates 
                2.430096             1.610775             1.396382 
                 alcohol 
                2.136067 

``` r
# checking for assumptions
par(mfrow = c(1,1))
plot(mod)
```

![](README_files/figure-commonmark/unnamed-chunk-10-1.png)

![](README_files/figure-commonmark/unnamed-chunk-10-2.png)

![](README_files/figure-commonmark/unnamed-chunk-10-3.png)

![](README_files/figure-commonmark/unnamed-chunk-10-4.png)

After removing fixed acidity the concerns about multicollinearity are
fixed, and the model holds the same assumptions as the model that
included fixed acidity.

Comparing the adjusted R-squared of both models can help determine which
model explains the most variation in the response variable, wine
quality. The Adjusted R-squared is 0.3561 for the model with fixed
acidity and for the model without fixed acidity is 0.3561. Because the
adjusted R-squared is unchanged and the model without fixed acidity
meets the model assumptions and does not have any multicollinearity
concerns, that will be the model used to determine if alcohol has the
strongest association with wine quality or if its another chemical
property.

### Stage 2 - White Wine

``` r
# loading package
library(car)

# forming model
mod <- lm(quality ~ ., data = white_wine)
summary(mod)
```


    Call:
    lm(formula = quality ~ ., data = white_wine)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -3.8348 -0.4934 -0.0379  0.4637  3.1143 

    Coefficients:
                           Estimate Std. Error t value Pr(>|t|)    
    (Intercept)           1.502e+02  1.880e+01   7.987 1.71e-15 ***
    fixed.acidity         6.552e-02  2.087e-02   3.139  0.00171 ** 
    volatile.acidity     -1.863e+00  1.138e-01 -16.373  < 2e-16 ***
    citric.acid           2.209e-02  9.577e-02   0.231  0.81759    
    residual.sugar        8.148e-02  7.527e-03  10.825  < 2e-16 ***
    chlorides            -2.473e-01  5.465e-01  -0.452  0.65097    
    free.sulfur.dioxide   3.733e-03  8.441e-04   4.422 9.99e-06 ***
    total.sulfur.dioxide -2.857e-04  3.781e-04  -0.756  0.44979    
    density              -1.503e+02  1.907e+01  -7.879 4.04e-15 ***
    pH                    6.863e-01  1.054e-01   6.513 8.10e-11 ***
    sulphates             6.315e-01  1.004e-01   6.291 3.44e-10 ***
    alcohol               1.935e-01  2.422e-02   7.988 1.70e-15 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.7514 on 4886 degrees of freedom
    Multiple R-squared:  0.2819,    Adjusted R-squared:  0.2803 
    F-statistic: 174.3 on 11 and 4886 DF,  p-value: < 2.2e-16

``` r
vif(mod)
```

           fixed.acidity     volatile.acidity          citric.acid 
                2.691435             1.141156             1.165215 
          residual.sugar            chlorides  free.sulfur.dioxide 
               12.644064             1.236822             1.787880 
    total.sulfur.dioxide              density                   pH 
                2.239233            28.232546             2.196362 
               sulphates              alcohol 
                1.138540             7.706957 

``` r
# checking for assumptions
par(mfrow = c(1,1))
plot(mod)
```

![](README_files/figure-commonmark/unnamed-chunk-11-1.png)

![](README_files/figure-commonmark/unnamed-chunk-11-2.png)

![](README_files/figure-commonmark/unnamed-chunk-11-3.png)

![](README_files/figure-commonmark/unnamed-chunk-11-4.png)

The diagnostics suggest that the linearity assumption is reasonably
satisfied, as the residual smoother is relatively flat except at the far
left, where a few low fitted values cause a slight upward deviation. The
constant variance assumption also appears reasonably satisfied based on
the relatively flat Scale-Location line. The Q-Q plot indicates
normality. In the residuals vs leverage plot, observation 2782 has
particularly high leverage, indicating that it may have a substantial
influence on the model. Observations 4746 and 3308 also have relatively
large negative residuals. These observations warrant further
investigation to determine their influence on the regression results.

Multicollinearity is another concern within the model. The VIF
coefficients indicate particularly high multicollinearity for density
(28) and residual sugar (13), with alcohol also showing an elevated VIF
(7.7). This suggests that some predictors share substantial overlapping
information, potentially making their regression coefficients unstable.
Density, in particular, has a large coefficient and appears
disproportionately influential in the ranking of predictors. Removing
density and refitting the model may help address this issue.

**Removing Density From Model**

``` r
# forming model
mod <- lm(quality ~ .-density, data = white_wine)
summary(mod)
```


    Call:
    lm(formula = quality ~ . - density, data = white_wine)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -3.9098 -0.4957 -0.0330  0.4666  3.1785 

    Coefficients:
                           Estimate Std. Error t value Pr(>|t|)    
    (Intercept)           2.0636371  0.3482321   5.926 3.32e-09 ***
    fixed.acidity        -0.0503197  0.0149092  -3.375 0.000744 ***
    volatile.acidity     -1.9583442  0.1138553 -17.200  < 2e-16 ***
    citric.acid          -0.0289483  0.0961455  -0.301 0.763360    
    residual.sugar        0.0256438  0.0025518  10.049  < 2e-16 ***
    chlorides            -0.9525303  0.5425208  -1.756 0.079194 .  
    free.sulfur.dioxide   0.0047672  0.0008391   5.682 1.41e-08 ***
    total.sulfur.dioxide -0.0008697  0.0003730  -2.331 0.019771 *  
    pH                    0.1651688  0.0825418   2.001 0.045444 *  
    sulphates             0.4193440  0.0973099   4.309 1.67e-05 ***
    alcohol               0.3626941  0.0112672  32.190  < 2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.756 on 4887 degrees of freedom
    Multiple R-squared:  0.2727,    Adjusted R-squared:  0.2713 
    F-statistic: 183.3 on 10 and 4887 DF,  p-value: < 2.2e-16

``` r
vif(mod)
```

           fixed.acidity     volatile.acidity          citric.acid 
                1.356128             1.128298             1.159884 
          residual.sugar            chlorides  free.sulfur.dioxide 
                1.435215             1.203645             1.744627 
    total.sulfur.dioxide                   pH            sulphates 
                2.153170             1.330912             1.056637 
                 alcohol 
                1.647117 

``` r
# checking for assumptions
par(mfrow = c(1,1))
plot(mod)
```

![](README_files/figure-commonmark/unnamed-chunk-12-1.png)

![](README_files/figure-commonmark/unnamed-chunk-12-2.png)

![](README_files/figure-commonmark/unnamed-chunk-12-3.png)

![](README_files/figure-commonmark/unnamed-chunk-12-4.png)

The residual plots still show the same patterns. The Residuals
vs. Fitted smoother is flat, and the Q-Q plots show the same tail
deviations, with linearity met and constant variance and normality
approximately met. The very high-leverage point flagged (observation
2782) no longer stands out, so no need to remove anything.

Multicollinearity was assessed using variance inflation factors (VIF),
with a threshold of 5. Variables were removed one at a time, starting
with the highest VIF. Density with vif = 28 was removed. After removal,
all the vif coefficients are below 5. This keeps the coefficient
estimates stable enough to compare the strength of each characteristic’s
association with quality.

Adjusted R-squared in the model including all predictors is 0.2803 and
the model without density is 0.271 which is a small cost for removing
the unstable density term.

## Findings on the Models

A multiple linear regression was fit for red wine with quality as the
response. Fixed acidity was removed because its VIF (7.8) exceeded the
threshold of 5, and all remaining VIFs were below 5. The model explains
about 36% of the variation in quality (adjusted R-squared = 0.356).
Alcohol, volatile acidity, sulphates, total sulfur dioxide, chlorides,
and pH were all statistically significant (p \< 0.05), and free sulfur
dioxide was marginally significant (p = 0.034) at the 0.05 level. Citric
acid, residual sugar, and density were not significant. Holding the
other variables constant, each additional percentage point of alcohol is
associated with an increase of about 0.29 points in predicted quality,
and each additional unit of volatile acidity is associated with a
decrease of about 1.08 points. Sulphates are positively associated with
quality, while total sulfur dioxide, chlorides, and pH are negatively
associated. Comparing standardized coefficients, alcohol (0.38) has the
strongest association with quality, followed by volatile acidity (-0.24)
and sulphates (0.19).

A multiple linear regression was also fit for white wine. Density was
removed because its VIF (28.2) indicated severe multicollinearity,
largely with residual sugar and alcohol, and after its removal all VIFs
were below 5. The model explains about 27% of the variation in quality
(adjusted R-squared = 0.271). Alcohol, volatile acidity, residual sugar,
free sulfur dioxide, sulphates, total sulfur dioxide, and pH, and fixed
acidity were significant at the 0.05 level. Chlorides and citric acid
were not significant. Holding the other variables constant, each
additional percentage point of alcohol is associated with an increase of
about 0.36 points in predicted quality, and each additional unit of
volatile acidity with a decrease of about 1.96 points. Alcohol has by
far the largest standardized coefficient (0.50), followed by volatile
acidity (-0.22) and residual sugar (0.15), while the remaining
significant predictors have small effects (standardized coefficients
below 0.10).

# Conclusion

The purpose of this analysis was to investigate which chemical
characteristics have the strongest relationships with the quality
ratings of red and white wines. Based on the results of both regression
models, alcohol has the strongest positive association with wine
quality, while volatile acidity has the strongest negative association.
This suggests that wines with higher alcohol content tend to receive
higher quality ratings, whereas wines with higher volatile acidity tend
to receive lower ratings. Monitoring alcohol levels and controlling
volatile acidity may be particularly important considerations when
evaluating wine characteristics associated with higher ratings. However,
these relationships should be interpreted as associations rather than
causal effects, since the analysis does not establish that changing
these characteristics will directly improve wine quality.

Throughout the modeling process, certain predictors were removed to
address multicollinearity. Fixed acidity was removed from the red wine
model, while density was removed from the white wine model because their
VIF values exceeded the acceptable threshold of 5. High
multicollinearity can make regression coefficients difficult to
interpret because predictors share overlapping information. Removing
these variables helped address this issue and improve the
interpretability of the models.

Although the models identified several meaningful relationships, there
are limitations to consider. The red wine model had an R-squared value
of approximately 0.36, while the white wine model had an R-squared value
of approximately 0.27. This indicates that the models explain
approximately 36% and 27% of the variation in wine quality,
respectively, meaning that most of the variation remains unexplained.
Additionally, wine quality is measured using discrete sensory ratings,
with most observations concentrated around ratings of 5 and 6. The
normality and constant variance assumptions were also only approximately
satisfied. The dataset does not account for other potentially
influential characteristics, such as grape variety, wine price, or
vineyard conditions.

When comparing the two types of wine, alcohol and volatile acidity
demonstrate important relationships with quality in both datasets.
However, sulphates and chlorides appear more influential in the red wine
model, while residual sugar has a more notable relationship with quality
in the white wine model. These differences suggest that certain chemical
characteristics may have varying relationships with the quality of red
and white wines.
