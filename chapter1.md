---
title_meta  : Unit 2
title       : Estimating measurement error in continuous variables
description : "Evaluating measurement error without a gold standard"
attachments :
  slides_link : http://jpsmonline.umd.edu/pluginfile.php/4810/mod_folder/content/0/SURV730-Unit-1-slides-2016-summer.pdf?forcedownload=1

--- type:NormalExercise lang:r xp:50 skills:7 key:39a225af5a
## A first look at the European Social Survey data 

In the [//www.europeansocialsurvey.org/data/download.html?r=7](European Social Survey round 7), data have been collected on 28,221 European's health (among many other topics). I have downloaded and recoded some of these data. You have them available in the R prompt as `ess7_health`. 

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

See the link above for the full questionnaire, and the [//www.europeansocialsurvey.org/docs/round7/survey/ESS7_appendix_a7_e03_0.pdf](ESS "variables and questions" Appendix) for a full list of variables and their names and possible values.

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

# Test the object, good_movies
# Notice that we didn't define any feedback here, this will cause automatically 
# generated feedback to be given to the student in case of an incorrect submission
test_object("ess7_health")

test_error()

```

--- type:NormalExercise lang:r xp:100 skills:7 key:34bb54f0b5
## Criterion correlations

In this exercise, you will start by reproducing the "criterion correlations" for discussing your health with a general practiction (GP) from the slides. Then you will look at other variables in the dataset.

Note that the example code uses [https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html](`dplyr`) pipes and the `select` function. This is not mandatory but just easier to read.

Possibly helpful: the [http://www.europeansocialsurvey.org/docs/round7/survey/ESS7_appendix_a7_e03_0.pdf](ESS "variables and questions" Appendix) for a full list of variables and their names and possible values.

*** =instructions
 - Correlate health discussion with the GP with the following variables: doing sports, sleeping problems, body-mass index, and age. Check this gives the same result as in the slides
 - Look at more "criterion" variables: eating fruit and vegetables, happiness, eduction level, and being born in the country of residence
 - Think about whether the correlations you find agree with the expectations of the "nomological net" around visiting the GP  

*** =hint
 - You can look at correlations with additional variables by adding these to the `select` statement
 - Remember R code is cAsE SENSITIVE and check for any typos


*** =sample_code
```{r}
# ess7_health is available in your workspace

# Correlate discussing health with GP with the following variables: doing sports, sleeping problems, body-mass index, and age
#   (the argument "pairwise.complete.obs" for the `cor` function deletes missing values for each pair of variables)
ess7_health %>% 
  select(dshltgp, dosprt, <complete this>) %>% 
  cor(use = "pairwise.complete.obs")

# Correlate with eating fruit and vegetables, happiness, eduction level, and being born in the country of residence

```

*** =solution
```{r}
# movie_selection is available in your workspace

# Correlate discussing health with GP with the following variables: doing sports, age, sleeping problems, and body-mass index
ess7_health %>% 
  select(dshltgp, dosprt, agea, slprl, BMI) %>% 
  cor(use = "pairwise.complete.obs")

# Correlate with eating fruit and vegetables, happiness, eduction level, and being born in the country of residence
ess7_health %>%
  select(dshltgp, etfruit, eatveg, happy, eisced, brncntr) %>%
  cor(use = "pairwise.complete.obs")

```

*** =sct
```{r}

# Test whether the student correctly used plot()
# Again, we use the automatically generated feedback here
test_function("cor")

# It's always smart to include the following line of code at the end of your SCTs
# It will check whether executing the student's code resulted in an error, 
# and if so, will cause the exercise to fail
test_error()

# Final message the student will see upon completing the exercise
success_msg("Good work!")
```
