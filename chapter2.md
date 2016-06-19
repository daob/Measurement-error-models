---
title_meta  : Unit 3
title       : Estimating measurement error in categorical variables
description : "Evaluating misclassification without a gold standard"
attachments :
  slides_link : http://jpsmonline.umd.edu/pluginfile.php/4813/mod_folder/content/0/SURV730-Unit-3-slides-2016-summer.pdf?forcedownload=1

--- type:NormalExercise lang:r xp:100 skills:7  key:39a225af5a
## Association measures for categorical variables

In your workspace you will find two crosstables from the slides:

* `tab_marijuana`: The crosstable between an interview and reinterview from a "large, national [United States-DLO] survey on drug use" (Biemer 2011, p. 56)
* `tab_gender_GP`: The crosstable between "gender" (gndr) and "discussing health with GP" (dshltgp)

First we are going to calculate some categorical data-association measures from these crosstables. On the right you see code for doing that for the measures discussed in the slides as well as two new ones (Yule's Q and &phi;). There are many more, which I won't get into but some are discussed in the references below:
* Yule's Q is explained on the Wikipedia page about "Goodman & Krusal's gamma" <https://en.wikipedia.org/wiki/Goodman_and_Kruskal%27s_gamma#Yule.27s_Q>
* The phi coefficient has its own wikipedia page: <https://en.wikipedia.org/wiki/Phi_coefficient>
* John Uebersax's page has a good overview of various association measures and their interrelatedness: <http://www.john-uebersax.com/stat/agree.htm>

*** =instructions 
- Look up the `phi` (&phi;) and `Yule` (Yule's Q) association measures online;
- Verify that the base code to the right gives the same estimates as are given for `tab_marijuana` in the slides;
- Adjust the code to the right to estimate association between `gndr` and `dshltgp` using the `tab_gender_GP` table;
- Verify that you get the same results as in the slides.

*** =hint
- Replace `tab_marijuana` with `tab_gender_GP` in the code to the right;
- In the call to `glm`, which uses Poisson regression to get at the log-odds ratio coefficient from a table, you may need to adjust the variable names `Question 1` and `Question 2` to correspond to the names in the `tab_gender_GP` table.

*** =solution
```{r}
# These are all from psych library:
tab_gender_GP %>% cohen.kappa
tab_gender_GP %>% tetrachoric
tab_gender_GP %>% phi
tab_gender_GP %>% Yule

# Different ways of getting the log-odds ratio from a table

# Calculate it by hand
my_log_odds_ratio <- function(tab) {
  odds_ratio <- (tab[1, 1] * tab[2, 2]) / (tab[1, 2] * tab[2, 1])
  log(odds_ratio)
}
tab_gender_GP %>% my_log_odds_ratio

# Using a Poisson model for the counts 
#    (useful when you want to use svyglm from the survey package
#      to get design-based complex sampling standard errors)
tab_gender_GP %>% as.data.frame %>% 
  glm(Freq ~ gndr * dshltgp, data = ., family = poisson) %>%
  summary

# As a loglinear model 
#     (useful when you want to test margins, can also use the survey
#      package's loglinear function to adjust for complex sampling)
# Note that the logodds ratio is 4*the loglinear interaction param.
tab_gender_GP %>% loglin(margin = list(1:2), param = TRUE)

```

*** =sample_code
```{r}
library(psych)

# These are all from psych library:
tab_marijuana %>% cohen.kappa
tab_marijuana %>% tetrachoric
tab_marijuana %>% phi
tab_marijuana %>% Yule

# Different ways of getting the log-odds ratio from a table


# Calculate it by hand
my_log_odds_ratio <- function(tab) {
  odds_ratio <- (tab[1, 1] * tab[2, 2]) / (tab[1, 2] * tab[2, 1])
  log(odds_ratio)
}
tab_marijuana %>% my_log_odds_ratio

# Using a Poisson model for the counts 
#    (useful when you want to use svyglm from the survey package
#      to get design-based complex sampling standard errors)
tab_marijuana %>% as.data.frame %>% 
  glm(Freq ~ Question.1 * Question.2, data = ., family = poisson) %>%
  summary

# As a loglinear model 
#     (useful when you want to test margins, can also use the survey
#      package's loglinear function to adjust for complex sampling)
# Note that the logodds ratio is 4*the loglinear interaction param.
tab_marijuana %>% loglin(margin = list(1:2), param = TRUE)


```

*** =pre_exercise_code
```{r}
library(psych)
library(vcd)
library(dplyr)

load(url("http://daob.nl/files/SURV730/tab_marijuana.rdata"))

load(url("http://daob.nl/files/SURV730/table_gender_GP.rdata"))

options(digits = 4)

```

*** =sct

```{r}
test_student_typed("tab_gender_GP %>% cohen.kappa")
test_student_typed("tab_gender_GP %>% tetrachoric")
test_student_typed("tab_gender_GP %>% phi")
test_student_typed("tab_gender_GP %>% Yule")
test_student_typed("tab_gender_GP %>% my_log_odds_ratio")

test_output_regex("gndr2:dshltgp2", 
  incorrect_msg = "There is a mistake in the Poisson regression (call to glm): you need to include the interaction between gender and GP.")
test_output_regex("0\\.416", 
  incorrect_msg = "I was looking for the correct log-odds ratio value (0.416..) in your output and could not find it. So something is still missing.")
test_output_regex("dshltgp\\.gndr", incorrect_msg = "There is a mistake in the loglinear model (call to loglin): you need to include the interaction between gender and GP.")
test_output_regex("-0.104", 
incorrect_msg = "I was looking for the correct loglinear model interaction estimate (0.104..) in your output and could not find it. So something is still missing there.")

test_error()

```

--- type:MultipleChoiceExercise lang:r xp:50 skills:7 

## Meaning of the association measures

All of the measures you calculated gave the association between two variables. What did these associations correspond to in terms of the concepts discussed in the previous unit?

*** =instructions
- `tab_marijuana`: criterion correlation, `tab_gender_GP`: criterion correlation
- `tab_marijuana`: test-retest reliability, `tab_gender_GP`: test-retest reliability
- `tab_marijuana`: criterion correlation, `tab_gender_GP`: test-retest reliability
- `tab_marijuana`: test-retest reliability, `tab_gender_GP`: criterion correlation


*** =hint
Pretend the categorical association measures are correlations and check the slides for unit 2.

*** =sct
```{r}
test_mc(correct = 4)
```








--- type:NormalExercise lang:r xp:100 skills:7  
## Simple latent class analysis

The `antireli` data frame contains the "tolerance scale" from the 1987 General Social Survey discussed in Hagenaars & McCutcheon (2002). 

The code to the right runs the two-class example given in the slides. 

More description of this type of latent class modeling is in the Biemer book as well as in [http://daob.nl/wp-content/papercite-data/pdf/oberski2016mixturemodels.pdf](this introductory text).

*** =instructions 
- With three dichotomous indicators, two classes is the maximum amount of information we can extract from these data. Verify this by fitting a three class model and encountering a problem.
- The "one class" model is actually a model of complete independence between the three indicators. Compare the AIC and BIC values of the one class and the two class models. Which model fits the data best?

*** =hint
- Change the nclass = 2 argument to a different number
- Check the output for the AIC and BIC and pick the model with the lowest value
- Check the output for alerts, warnings, etc. 

*** =solution
```{r}
poLCA(cbind(Y1, Y2, Y3) ~ 1, data = antireli, nclass = 1)
poLCA(cbind(Y1, Y2, Y3) ~ 1, data = antireli, nclass = 2)
poLCA(cbind(Y1, Y2, Y3) ~ 1, data = antireli, nclass = 3)
```

*** =sample_code
```{r}
library(poLCA)

# This calls the poLCA function to fit a latent class model 
#    on the three indicators Y1, Y2, Y3
#    with two classes (nclass = 2).
M2 <- poLCA(cbind(Y1, Y2, Y3) ~ 1, data = antireli, nclass = 2)
M2
```

*** =pre_exercise_code
```{r}
library(poLCA)
library(dplyr)

antireli <- read.csv("http://daob.nl/files/SURV730/antireli_data.csv")
set.seed(42)
options(digits = 4)
```

*** =sct

```{r}
test_output_regex("", 
  incorrect_msg = "There is a mistake in the Poisson regression (call to glm): you need to include the interaction between gender and GP.")
test_output_regex("0\\.416", 
  incorrect_msg = "I was looking for the correct log-odds ratio value (0.416..) in your output and could not find it. So something is still missing.")
test_output_regex("dshltgp\\.gndr", incorrect_msg = "There is a mistake in the loglinear model (call to loglin): you need to include the interaction between gender and GP.")
test_output_regex("-0.104", 
incorrect_msg = "I was looking for the correct loglinear model interaction estimate (0.104..) in your output and could not find it. So something is still missing there.")

test_error()

```