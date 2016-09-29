---
title       : Modelling
description : Exercises to Test Concepts in Part 2 of Data Science Workshop



--- type:NormalExercise lang:r xp:100 skills:1 key:99f5dc3cd37ec9a6b55f72608d8a0ae79735f68a
## Model Selection

Use graphics to assess whether a linear model describing how player ranking is related to their tournaments played. 



*** =instructions
- The `atp2015` dataset is in the workspace.
- Create a summary version of the data that counts the tournaments entered and the average player rank in the season.
- Create a scatterplot of tournaments played against player rank.
- Is the relationship linear?


*** =hint
Use `dplyr` and `summarise`

*** =sample_code
```{r}
atp2015 <- read.csv(file = "http://on-the-t.com/assets/data/atp2015.csv")
```

*** =solution
```{r}
atp2015 <- atp2015 %>%
	group_by(player) %>%
	summarise(
		tournaments = n_distinct(name),
		rank = mean(player_rank, na.rm = TRUE)
	)

atp2015 %>%
	ggplot(aes(y = tournaments, x = rank)) +
	geom_point()
```

*** =sct
```{r}
test_error()
success_msg("Did you reach a conclusion? Great, you are ready for the next exercise.")
```


--- type:NormalExercise lang:r xp:100 skills:1 key:bf4ff8dc0a

##  Linear Model

Fit a linear model of tournaments played against mean player rank and assess the diagnostic plots. 



*** =instructions
- Assume the summary dataset for `atp2015` is available in the workspace
- Fit a linear model of `tournaments` on `rank`
- Examine the diagnostic plots
- Determine if there are any variables of concern


*** =hint
Use `lm` and `plot`.

*** =pre_exercise_code
```{r}
atp2015 <- read.csv(file = "http://on-the-t.com/assets/data/atp2015.csv")

atp2015 <- atp2015 %>%
	group_by(player) %>%
	summarise(
		tournaments = n_distinct(name),
		rank = mean(player_rank, na.rm = TRUE)
	)
```



*** =solution
```{r}
fit <- lm(tournaments ~ rank, data = atp2015)

plot(fit)
```

*** =sct
```{r}
test_error()
success_msg("Ready for the next exercise? Excellent!")
```



--- type:NormalExercise lang:r xp:100 skills:1 key:895c70acb4

##  Influence

Fit a linear model with and without the influential point found in the previous exercise. How much do he model coefficients change?




*** =instructions
- Assume the summary dataset for `atp2015` is available in the workspace
- Fit and save both linear models
- Use `coef` to assess the difference in fit
- What accounts for the differences?


*** =hint
Use `coef`

*** =pre_exercise_code
```{r}
atp2015 <- read.csv(file = "http://on-the-t.com/assets/data/atp2015.csv")

atp2015 <- atp2015 %>%
	group_by(player) %>%
	summarise(
		tournaments = n_distinct(name),
		rank = mean(player_rank, na.rm = TRUE)
	)
```



*** =solution
```{r}
fit <- lm(tournaments ~ rank, data = atp2015)

fit1 <- lm(tournaments ~ rank, data = atp2015,
	subset = player != "Taylor Fritz")

coef(fit)

coef(fit1)
```

*** =sct
```{r}
test_error()
success_msg("Keep it up!")
```


--- type:NormalExercise lang:r xp:100 skills:1 key:3559047d13

##  Formula

Are upsets more common on some surfaces than others?


*** =instructions
- Create a variable for upsets based on player ranking in the `atp2015` dataset.
- Create a formula that you could use to assess the relationship between upsets and surface
- Save the formula as the object `f`


*** =hint
Use `mutate`

*** =pre_exercise_code
```{r}
atp2015 <- read.csv(file = "http://on-the-t.com/assets/data/atp2015.csv")
```


*** =solution
```{r}
atp2015 <- atp2015 %>%
	mutate(
		upset = winner == 1 & player_rank > opponent_rank
	)

f <- upset ~ surface	
```

*** =sct
```{r}
test_object("f")
test_error()
success_msg("You are really getting this!")
```


--- type:NormalExercise lang:r xp:200 skills:1 key:b6255776bc

## Upset Model 

Use an appropriate model to fit and summarise the results of the difference in the likelihood of an upset by surface.


*** =instructions
- Assume that the variable `upset` is already available in the `atp2015` dataset.
- Fit an appropriate model for the `upset` outcome.
- Summarise the results and assign them to the object `upset_summary`
- How likely is an upset? Does it vary by surface?


*** =hint
Use `glm`

*** =pre_exercise_code
```{r}
atp2015 <- read.csv(file = "http://on-the-t.com/assets/data/atp2015.csv")

atp2015 <- atp2015 %>%
	mutate(
		upset = as.numeric(winner == 1 & player_rank > opponent_rank)
	)
```


*** =solution
```{r}
fit <- glm(upset ~ surface, 
	data = atp2015,
	family = binomial)

upset_summary <- summary(fit)

upset_summary

exp(coef(fit)[1]) / (1 + exp(coef(fit)[1]))
```

*** =sct
```{r}
test_object("upset_summary")
test_error()
success_msg("You got it! Congratulations!")
```


