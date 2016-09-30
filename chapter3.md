---
title       : Missing Data
description : Exercises to Test Concepts in Part 3 of Data Science Workshop



--- type:NormalExercise lang:r xp:100 skills:1 key:99f5dc3cd37ec9a6b55f72608d8a0ae79735f68a
## Summarise Missingness

The dataset `atp_players` has information about the nationality, height (cm) and average ace count for players competing in 2015. Examine the extent of missing data in this dataset.


*** =instructions
- The `atp_player` dataset is in the workspace
- Use the `mice` package to summarise the missing patterns for `ht` and `ace_avg`	
- What do you find?
- Assign your result to the object `missing`


*** =hint
Use `md.pattern`.

*** =pre_exercise_code
```{r}
library(mice)

atp_players <- read.csv(file = "http://on-the-t.com/assets/data/atp_players.csv")
```

*** =solution
```{r}
missing <- md.pattern(atp_players[,c("ht", "ace_avg")])

missing
```

*** =sct
```{r}
test_object("missing")
test_error()
success_msg("Great work!")
```


--- type:NormalExercise lang:r xp:100 skills:1 key:bf4ff8dc0a

##  Systematic Missingness

Is there evidence that missing height is more common for players from certain nations?


*** =instructions
- Assume the summary dataset for `atp_players` is available in the workspace
- Use `dplyr` to look at the frequency of missing height values by nation (`ioc`)
- Assign your result to `missing_by_country` and call the frequency `freq`
- Interpret your findings


*** =hint
Use `is.na` and `group_by`

*** =pre_exercise_code
```{r}
library(dplyr)

atp_players <- read.csv(file = "http://on-the-t.com/assets/data/atp_players.csv")
```



*** =solution
```{r}
missing_by_country <- atp_players %>%
	group_by(ioc) %>%
	summarise(
		freq = mean(is.na(ht)) 
	)

missing_by_country
```

*** =sct
```{r}
test_object("missing_by_country")
test_error()
success_msg("Excellent job!")
```



--- type:NormalExercise lang:r xp:100 skills:1 key:895c70acb4

##  Single Imputation

Use linear regression to create a single imputation model for height.

*** =instructions
- Assume the summary dataset for `atp_players` is available in the workspace
- Fit a linear model on mean aces 
- Obtain the adjusted R-squared and save it as the object `R2`


*** =hint
Use `lm`


*** =pre_exercise_code
```{r}
atp_players <- read.csv(file = "http://on-the-t.com/assets/data/atp_players.csv")
```


*** =solution
```{r}
fit <- lm(ht ~ ace_avg, data = atp_players)

summary(fit)

R2 <- summary(fit)$adj.r.squared
R2
```

*** =sct
```{r}
test_object("R2")
test_error()
success_msg("You are really getting this!")
```


--- type:NormalExercise lang:r xp:200 skills:1 key:3559047d13

##  Evaluate Imputed Values

Does the distribution for the imputed values look reasonable?


*** =instructions
- Assume the `fit` linear model for height and `atp_players` are available
- Obtain predictions for the missing values
- Use `ggplot2` to compare the density of the missing and observed values
- Evaluate whether they are reasonable


*** =hint
Use `geom_density`

*** =pre_exercise_code
```{r}
library(dplyr)
library(ggplot2)

atp_players <- read.csv(file = "http://on-the-t.com/assets/data/atp_players.csv")

fit <- lm(ht ~ ace_avg, data = atp_players)
```

*** =solution
```{r}
atp_players$missing <- factor(is.na(atp_players$ht),
	levels = c(FALSE, TRUE),
	labels = c("Known", "Missing"))

imputed <- predict(fit, newdata = atp_players[is.na(atp_players$ht), ])

atp_players$ht[is.na(atp_players$ht)] <- imputed

atp_players %>%
	ggplot(aes(x = ht)) +
	geom_density() +
	facet_wrap( ~ missing)
```

*** =sct
```{r}
test_error()
success_msg("Keep it up!")
```


--- type:NormalExercise lang:r xp:200 skills:1 key:b6255776bc

## Multiple Imputation

Conduct a multiple imputation for the variables in the `atp_players` dataset and get a pooled estimate for average heights by country. 


*** =instructions
- Assume that the `atp_players` dataset is available
- Use `mice` with 3 imputed datasets and defaults otherwise
- Use `lm.mids` to estimate height by country
- Save the pooled linear model as the object `pooled_height`


*** =hint
Use `pool`

*** =pre_exercise_code
```{r}
library(mice)

atp_players <- read.csv(file = "http://on-the-t.com/assets/data/atp_players.csv")
```

*** =solution
```{r}
imputed <- mice(atp_players, m = 3)

fits <- lm.mids(
	ht ~ ioc,
	data = imputed
)

pooled_height <- pool(fits)

pooled_height
```

*** =sct
```{r}
test_object("pooled_height")
test_error()
success_msg("That's fantastic! You've mastered this!")
```


