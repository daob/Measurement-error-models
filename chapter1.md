---
title_meta  : Unit 2
title       : Estimating measurement error in continuous variables
description : "Evaluating measurement error without a gold standard"
attachments :
  slides_link : http://jpsmonline.umd.edu/pluginfile.php/4810/mod_folder/content/0/SURV730-Unit-1-slides-2016-summer.pdf?forcedownload=1

--- type:NormalExercise lang:r xp:50 skills:7  key:39a225af5a
## A first look at the European Social Survey data 

In the [European Social Survey round 7](http://www.europeansocialsurvey.org/data/download.html?r=7), data have been collected on 28,221 European's health (among many other topics). I have downloaded and recoded some of these data. You have them available in the R prompt as `ess7_health`. 

This dataset has the following variables in it:

*  dshltgp : DISCUSSED HEALTH, LAST 12 MONTHS: GENERAL PRACTITIONER 
*  dosprt : DO SPORTS OR OTHER PHYSICAL ACTIVITY, HOW MANY OF LAST 7 DAYS
*  alcfreq : HOW OFTEN DRINK ALCOHOL
*  etfruit : HOW OFTEN EAT FRUIT, EXCLUDING DRINKING JUICE
*  eatveg : HOW OFTEN EAT VEGETABLES OR SALAD, EXCLUDING POTATOES
*  health : SUBJECTIVE GENERAL HEALTH
*  happy : HOW HAPPY ARE YOU
*  slprl : SLEEP WAS RESTLESS, HOW OFTEN PAST WEEK
*  agea : Age
*  eisced : Education level
*  brncntr : BORN IN COUNTRY
*  smoke : Whether person currently smokes (recode of ESS variable CGTSMKE)
*  BMI : Body mass index (recode of ESS variables HEIGHT and WEIGHT)
*  health_problems : Number of health problems (recode of ESS variables HLTHPRxx)

You may want to *copy-paste this list into a text file for future reference*.

See the link above for the full questionnaire, and the [ESS "variables and questions" Appendix](http://www.europeansocialsurvey.org/docs/round7/survey/ESS7_appendix_a7_e03_0.pdf) for a full list of variables and their names and possible values.

*** =instructions 

- Check out the summary of `ess7_health`
- Familiarize yourself with the R environment here
- Once you are ready, click "submit"

*** =hint
 - Use the `summary` function to summarize `ess7_health`. 

*** =solution
```{r}
summary(ess7_health)
```

*** =sample_code
```{r}
# You can execute code by typing it here and pressing Ctrl+Enter
# Type your code below this line before you submit: 
```

*** =pre_exercise_code
```{r}
library(psych)
library(lavaan)
library(semTools)
library(dplyr)

# The pre exercise code runs code to initialize the user's workspace. You can use it for several things:

# 1. Preload a dataset. The code below will read the csv that is stored at the URL's location.
# The movies variable will be available in the user's console.
load(url("http://daob.nl/files/SURV730/ess7_health.rdata"))

options(digits = 4)

```

*** =sct

```{r}
# The sct section defines the Submission Correctness Tests (SCTs) used to
# evaluate the student's response. All functions used here are defined in the 
# testwhat R package. Documentation can also be found at github.com/datacamp/testwhat/wiki

# Test whether the function str is called with the correct argument, object
# If it is not called, print something informative
# If it is called, but called incorrectly, print something else
test_function("summary", args = "object",
              not_called_msg = "You didn't call `summary()`!",
              incorrect_msg = "You didn't call `summary(object = ...)` with the correct argument, `object`.")

# Notice that we didn't define any feedback here, this will cause automatically 
# generated feedback to be given to the student in case of an incorrect submission
test_object("ess7_health")

test_error()

```

--- type:NormalExercise lang:r xp:100 skills:7  key:34bb54f0b5
## Criterion correlations

In this exercise, you will start by reproducing the "criterion correlations" for discussing your health with a general practiction (GP) from the slides. Then you will look at other variables in the dataset.

Note that the example code uses `dplyr` pipes, the `select` function, and friends. This is not mandatory but just easier to read. See <https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html> if you'd like to find out more about using `dplyr`.

Possibly helpful: the [ESS "variables and questions" Appendix](http://www.europeansocialsurvey.org/docs/round7/survey/ESS7_appendix_a7_e03_0.pdf) for a full list of variables and their names and possible values.

*** =instructions
 - Correlate health discussion with the GP with the following variables: doing sports, sleeping problems, body-mass index, and age. Check this gives the same result as in the slides
 - Look at more "criterion" variables: eating fruit and vegetables, happiness, eduction level, and being born in the country of residence
 - Think about whether the correlations you find agree with the expectations of the "nomological net" around visiting the GP  

*** =hint
 - You can look at correlations with additional variables by adding these to the `select` statement
 - Remember R code is cAsE SENSITIVE and check for any typos (note most variable names here are in lower case)
 - If you need to know more about the variables, check out the Appendix linked above 
 - Check that the code provided to output only the correlations with GP, in alphabetical order, is used for both parts

*** =pre_exercise_code
```{r}
library(psych)
library(lavaan)
library(semTools)
library(dplyr)

# The pre exercise code runs code to initialize the user's workspace. You can use it for several things:

# 1. Preload a dataset. The code below will read the csv that is stored at the URL's location.
# The movies variable will be available in the user's console.
load(url("http://daob.nl/files/SURV730/ess7_health.rdata"))

options(digits = 4)

```

*** =sample_code
```{r}
# ess7_health is available in your workspace

# Correlate discussing health with GP with the following variables: 
#	doing sports, sleeping problems, body-mass index, and age
#   (the argument "pairwise.complete.obs" for the `cor` function 
#		deletes missing values for each pair of variables)

criterion_slides <- ess7_health %>% select(dshltgp, dosprt, <complete this>) %>% cor(use = "pairwise.complete.obs")

# To make it easier to check your answer, 
#	sort by name and output only correlations with GP: 
criterion_slides <- criterion_slides["dshltgp", sort(colnames(criterion_slides)[-1])] 
criterion_slides

# Now correlate with eating fruit and vegetables, happiness, 
#	education level, and being born in the country of residence
# And assign the result to criterion_extra

criterion_extra <- <complete this>


# To make it easier to check your answer, 
#	sort by name and output only correlations with GP: 
criterion_extra <- criterion_extra["dshltgp", sort(colnames(criterion_extra)[-1])] 
criterion_extra
```

*** =solution
```{r}
# movie_selection is available in your workspace

# Correlate discussing health with GP with the following variables: doing sports, age, sleeping problems, and body-mass index
criterion_slides <- 
	ess7_health %>% 
	  select(dshltgp, dosprt, agea, slprl, BMI) %>% 
	  cor(use = "pairwise.complete.obs") 
criterion_slides <- criterion_slides["dshltgp", sort(colnames(criterion_slides)[-1])] 

# Correlate with eating fruit and vegetables, happiness, eduction level, and being born in the country of residence
criterion_extra <- 
	ess7_health %>%
  		select(dshltgp, etfruit, eatveg, happy, eisced, brncntr) %>%
  		cor(use = "pairwise.complete.obs")
criterion_extra <- criterion_extra["dshltgp", sort(colnames(criterion_extra)[-1])] 

```

*** =sct
```{r}
criterion_slides <- 
	ess7_health %>% 
	  select(dshltgp, dosprt, agea, slprl, BMI) %>% 
	  cor(use = "pairwise.complete.obs") 
criterion_slides <- criterion_slides["dshltgp", sort(colnames(criterion_slides)[-1])] 

# Correlate with eating fruit and vegetables, happiness, eduction level, and being born in the country of residence
criterion_extra <- 
	ess7_health %>%
  		select(dshltgp, etfruit, eatveg, happy, eisced, brncntr) %>%
  		cor(use = "pairwise.complete.obs")
criterion_extra <- criterion_extra["dshltgp", sort(colnames(criterion_extra)[-1])] 

# Test whether the student correctly used plot()
# Again, we use the automatically generated feedback here
test_function("cor")

test_object("criterion_slides")

test_object("criterion_extra")

# It's always smart to include the following line of code at the end of your SCTs
# It will check whether executing the student's code resulted in an error, 
# and if so, will cause the exercise to fail
test_error()

# Final message the student will see upon completing the exercise
success_msg("Good work!")
```


--- type:MultipleChoiceExercise lang:r xp:50 skills:7  key:9576140612

## Interpreting the criterion correlations

Looking at the criterion correlations, one of them is close to zero. Which one is that?

*** =instructions
- Eating fruit
- Born in country
- Happiness
- Education level

*** =hint
Look at the `criterion_extra` output from the previous exercise

*** =sct
```{r}
test_mc(correct = 2)

success_msg("That is right, it is also not clear what we would have expected here. Maybe immigrants are unhealthier and need more treatment (negative association), or maye they are less able to find their way to the GP (positive association).")
```


--- type:MultipleChoiceExercise lang:r xp:50 skills:7  key:e47168f111

## Interpreting the criterion correlations, cont.

What can we conclude from the fact that the criterion correlation with "born in country" is so small?

*** =instructions
- The validity of this question is low 
- The reliability of this question is low
- The criterion variable has low validity 
- Nothing

*** =hint
Think back to the disadvantages of criterion validity from the slides.

*** =sct
```{r}
test_mc(correct = 4)

success_msg("Correct. It just doesn't really help to know this at all.")
```



--- type:NormalExercise lang:r xp:100 skills:7   key:64b7b63cd3
## Internal consistency with factor analysis

As explained in Unit 2, when there are no repeated measures of the same _question_, we can use repeated measures 
of the same _concept_ to get at the "internal consistency". 

This code uses the `cfa` function from the `lavaan` library, which is very flexible. The [model syntax](http://lavaan.ugent.be/tutorial/syntax1.html) is relatively easy to read: in this example, we only need to define which variables are regressed on the latent variable (concept) `health_latent`. This is done by using the `=~` operator, which means "is measured by".

Note that the results here may be different from those in the slides. 

If you would like to learn more on doing confirmatory factor analysis in R using `lavaan`, the tutorial at <http://lavaan.ugent.be/tutorial/cfa.html> is a good place to start. I can also recommend the book by Beaujean (see [here](http://lavaan.ugent.be/resources/books.html)) with accompanying R code.

*** =instructions
 - Estimate the internal consistency coefficient of "discussing health with GP" based on the same three indicators as in the slides: GP, self-rated health, and sleeping problems.
 - Change the code to also use the number of reported health problems as an indicator 
 - Interpret the "Std.all" coefficients as the internal consistency  coefficients 

*** =hint
 
*** =pre_exercise_code
```{r}
library(psych)
library(lavaan)
library(semTools)
library(dplyr)
load(url("http://daob.nl/files/SURV730/ess7_health.rdata"))

options(digits = 4)

```

*** =sample_code
```{r}
library(lavaan)

# Use the cfa function from the lavaan package to fit a 
#	confirmatory factor analysis (CFA) model that gives the 
#	internal consistency  coefficients as "standardized loadings" (or "coefficients")  

# The latent concept "health_latent" is posited to be 
#		the common cause of these three variables ("indicators")
model <- "health_latent =~ dshltgp + health + slprl"

# Fit the model in lavaan and assign the result to a variable
fit <- cfa(model, data = ess7_health)

# Print the output of the CFA 
# 	standardized loadings will be the consistency coefficients 
summary(fit, standardized = TRUE)

# A different way of getting at these is:
consistencies <- standardizedSolution(fit) %>% filter(op == "=~")
consistencies

# Change the analysis so health_problems is also included as an indicator.

```

*** =solution
```{r}
# Use the cfa function from the lavaan package to fit a 
#	confirmatory factor analysis (CFA) model that gives the 
#	internal consistency  coefficients as "standardized loadings" (or "coefficients")  

# The latent concept "health_latent" is posited to be 
#		the common cause of these three variables ("indicators")
model <- "health_latent =~ dshltgp + health + slprl + health_problems"

# Fit the model in lavaan and assign the result to a variable
fit <- cfa(model, data = ess7_health)

# Print the output of the CFA 
# 	standardized loadings will be the consistency coefficients 
summary(fit, standardized = TRUE)

# A different way of getting at these is:
consistencies <- standardizedSolution(fit) %>% filter(op == "=~")
consistencies

```

*** =sct
```{r}

test_function("cfa")

test_function("summary", args = "object",
              not_called_msg = "You didn't call `summary()`!",
              incorrect_msg = "You didn't call `summary(object = ...)` with the correct argument, `object`.")

test_object("fit")

test_object("consistencies")

test_error()

success_msg("Good work!")
```


--- type:MultipleChoiceExercise lang:r xp:50 skills:7   key:2fc7f7d28e

## Interpreting confirmatory factor analysis

Assuming that the CFA model is true, which indicator had the highest internal consistency?

*** =instructions
- Discussing health with GP
- Self-reported health
- Sleeping problems
- Health problems

*** =hint
- If all the assumptions of this method hold, 
the internal consistency  coefficients are equal to the standardized loadings
- These are reported by lavaan as "Std.all" coefficients under "=~"

*** =sct
```{r}
test_mc(correct = 2)

success_msg("Correct.")
```



--- type:MultipleChoiceExercise lang:r xp:50 skills:7   key:15588e64e7

## Interpreting confirmatory factor analysis, cont.

After an additional indicator was used to measure the latent concept "health_latent", the internal consistency  estimate for "GP" changed. What could be the explanation?

*** =instructions
- Sampling fluctuations
- Violation of assumptions
- Both sampling and assumption violations
- None of the above

*** =hint
- The internal consistency estimate is completely based on the consistency among the indicators: the higher this is, the higher the estimate will be. 


*** =sct
```{r}
test_mc(correct = 3)

success_msg("If the model is true (so no assumptions are violated) and there is infinite data, using more indicators will not change the consistency estimate. So either sampling or assumption violations can cause this change.")
```


--- type:MultipleChoiceExercise lang:r xp:50 skills:7    key:477d905861

## Multitrait-multimethod models: looking at correlations

The correlations between the variables measured in the multitrait-multimethod (MTMM) experiment from the slides from Saris & Gallhofer are available in the workspace as `R`. The "correlation plot" to the right shows these as larger or smaller circles. The larger the circle, the stronger the correlation (positive or negative).

The variables have been renamed to show which "trait" (survey question) and "method" (way of asking the question) they correspond to. For example, T1M1 might refer to "satisfaction with the economy" (T1) measured on a four point scale (M1), and T1M2 to "satisfaction with the economy" (T1) measured on an eleven point scale (M2).

Which correlations from the MTMM experiment are strongest?

*** =instructions
- Those between different questions measured in the same way ("heterotrait-monomethod")
- Those between the same questions measured in different ways ("monotrait-heteromethod")
- Those between different questions measured in different ways ("heteromethod-heterotrait")
- All the correlations are the same

*** =hint
Try to find the different groups of correlations mentioned in the picture. Which group has the largest circles overall? 

*** =pre_exercise_code

```{r}
cors <- 
"1000
 481  1000
 373  552  1000
-626 -422 -410  1000
-429 -663 -532  642  1000
-453 -495 -669  612  693  1000
-502 -347 -332  548  436  438  1000
-370 -608 -399  429  653  466  556  1000
-336 -406 -566  406  471  638  514  558  1000"

library(lavaan)
R <- lavaan::getCov(cors)/1000

colnames(R) <- rownames(R) <-
 paste0(paste0("T", rep(1:3, 3)),
         paste0("M", rep(1:3, each=3)))

corrplot::corrplot(R)         
```

*** =sct
```{r}
test_mc(correct = 2)

success_msg("Indeed that seems the case for these data, and this seems like a good thing. This classification of groups of correlations is due to Campbell and Fiske (1959), who based their assessment of the measurement properties purely on the type of consideration you have just done.")
```

--- type:NormalExercise lang:r xp:100 skills:7   key:4d0e7de841

## Multitrait-multimethod models

MTMM _models_ can be applied to MTMM _data_ to obtain estimates of the reliability coefficients and method effects. 

Since the MTMM _design_ crosses "traits" (survey questions in our case) with "methods" (ways of asking those questions), this is a matter of specifying the right indicator for each factor in a factor analysis.

This time, due to the study design, the interpretation of the standardized factor loadings is that they represent "reliability coefficients" and "method effects" of the questions studied. 

Please read the instructions below and in the comments to the right very carefully!

*** =instructions
- Formulate the MTMM model for the correlations in the MTMM correlation matrix `R` by replacing the ... dots in the code to the right
- Run the model to obtain estimates using the ready-made code
- Interpret the results by looking at the standardized estimates. 
- The standardized coefficients on the Tx factors are the reliability coefficient estimates
- The standardized coefficients on the Mx factors are the method effect estimates


*** =hint
- Read the instructions in the comments to the right very carefully
- Use the names of the variables: "T1M1" belongs to trait factor T1 and method factor M1, for instance
- Make sure the method indicators each have their loading set to 1 by using the 1* suggested
- Make sure the original code for the model is not accidentally removed: only change the dots.


*** =pre_exercise_code

```{r}
cors <- 
"1000
 481  1000
 373  552  1000
-626 -422 -410  1000
-429 -663 -532  642  1000
-453 -495 -669  612  693  1000
-502 -347 -332  548  436  438  1000
-370 -608 -399  429  653  466  556  1000
-336 -406 -566  406  471  638  514  558  1000"

library(lavaan)
R <- lavaan::getCov(cors)/1000

colnames(R) <- rownames(R) <-
 paste0(paste0("T", rep(1:3, 3)),
         paste0("M", rep(1:3, each=3)))

```

*** =sample_code
```{r}
# Show the correlation matrix:
R

# Define the MTMM model below (see slides)
model <- "
  # Fill in the measurement of the traits and methods.
  # Note that the names are T1M1, T1M2, etc.

  # Model for the methods: prefix every variable with 1*, 
  #			e.g use M1 =~ 1*T1M1 + ...
  M1 =~ ...    						# Replace the dots 
  M2 =~ ...
  M3 =~ ...

  # Model for the traits: don't prefix indicators with anything
  T1 =~ ...
  T2 =~ ...
  T3 =~ ...

  # This specifies that the traits correlate. You can leave it as-is
  T1 ~~ T2 + T3
  T2 ~~ T3

  # This identifies the latent trait variables by 
  #		standardizing them to variance 1. You can leave it as-is.
  T1 ~~ 1*T1
  T2 ~~ 1*T2
  T3 ~~ 1*T3
"

# Fit the model as a CFA using lavaan
fit <- lavaan(model, sample.cov = R, 
              sample.nobs = 424, 
              auto.cov.lv.x = FALSE, 
              auto.fix.first = FALSE, auto.var = TRUE)
                   
# Summarize the results
summary(fit, standardize = TRUE)

# The standardized loadings (those with op "=~") are 
#		the reliability coefficients (for Tx) 
#   	and method effects (for Mx):
std_estimates <- standardizedSolution(fit) %>% arrange(lhs, rhs)
std_estimates
```

*** =solution
```{r}
# Show the correlation matrix:
R

# Define the MTMM model below (see slides)
model <- "
M1 =~ 1*T1M1 + 1*T2M1 + 1*T3M1
M2 =~ 1*T1M2 + 1*T2M2 + 1*T3M2
M3 =~ 1*T1M3 + 1*T2M3 + 1*T3M3

T1 =~ T1M1 + T1M2 + T1M3
T2 =~ T2M1 + T2M2 + T2M3
T3 =~ T3M1 + T3M2 + T3M3

T1 ~~ T2 + T3
T2 ~~ T3

T1 ~~ 1*T1
T2 ~~ 1*T2
T3 ~~ 1*T3"

# Fit the model as a CFA using lavaan
fit <- lavaan(model, sample.cov = R, 
              sample.nobs = 424, 
              auto.cov.lv.x = FALSE, 
              auto.fix.first = FALSE, auto.var = TRUE)
                   
# Summarize the results
summary(fit, standardize = TRUE)

# The standardized loadings are the reliability coefficients (for Tx) 
#   	and method effects (for Mx):
std_estimates <- standardizedSolution(fit) %>% arrange(lhs, rhs)
std_estimates
```

*** =sct
```{r}
test_object("std_estimates")

test_error()

success_msg("Awesome job. MTMM modeling is not easy but now you have it down already!")
```
