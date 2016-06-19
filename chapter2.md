---
title_meta  : Unit 3
title       : Estimating measurement error in categorical variables
description : "Evaluating misclassification without a gold standard"
attachments :
  slides_link : http://jpsmonline.umd.edu/pluginfile.php/4813/mod_folder/content/0/SURV730-Unit-3-slides-2016-summer.pdf?forcedownload=1

--- type:NormalExercise lang:r xp:50 skills:7  key:39a225af5a
## Association measures for categorical variables


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

test_error()

```

