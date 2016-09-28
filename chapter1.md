---
title       : Exploratory Data Analysis
description : Exercises to Test Concepts in Part 1 of Data Science Workshop



--- type:NormalExercise lang:r xp:100 skills:1 key:99f5dc3cd37ec9a6b55f72608d8a0ae79735f68a
## Importing

A CSV of the 2015 activity of Novak Djokovic is stored on the `on-the-t.com' website. This exercise tests your ability to read-in the file.


*** =instructions
- Import the `djokovic2015` CSV under the assets/data folder on the website.
- Save it as the data frame `djokovic`


*** =hint
Use `read.csv`

*** =sample_code
```{r}
```

*** =solution
```{r}
djokovic <- read.csv("http://on-the-t.com/assets/data/djokovic2015.csv")
```

*** =sct
```{r}
test_object("djokovic")
test_error()
success_msg("Good work!")
```

--- type:MultipleChoiceExercise lang:r xp:50 skills:5 key:ca7104d708e3098c797eafab9a471873b84a434e
## Inspect Data

Suppose you are interested in getting an overall sense of the data contents of a dataset. Which of the following functions would _not_ be a good choice?

*** =instructions

- summary
- mean
- str

*** =hint
Recall some of the functions that tell us about the observations and columns of a dataset.

*** =sct
```{r}

msg1 <- "No. This would be a good choice for inspecting the data."
msg2 <- "Correct! The mean function is a specific way of summarising a continuous variable not a dataset."
msg3 <- "Try again. The str (for structure) is a big help when inspecting data."

test_mc(correct = 2, feedback_msgs = c(msg1, msg2, msg3)) 
```



--- type:NormalExercise lang:r xp:100 skills:1 key:79c574204a

## Data Wrangling 

Determine Djokovic's win record in 2015.


*** =instructions
- Assume `djokovic` is available and `dplyr` loaded.
- Find the variable that indicates wins.
- Use `dplyr` to obtain the count of wins and losses.
- Create a  data frame with these counts called `wins`.

*** =hint
Use `group_by`.

*** =pre_exercise_code
```{r}
library(dplyr)

djokovic <- read.csv("http://on-the-t.com/assets/data/djokovic2015.csv")
```



*** =solution
```{r}
wins <- djokovic %>%
	group_by(winner) %>%
	summarise(n = n()) 

wins
```

*** =sct
```{r}
test_object("wins")
test_error()
success_msg("Excellent Job!")
```




--- type:NormalExercise lang:r xp:100 skills:1 key:1a5c9c5c70

## Graphics

Plot Djokovic's 2015 wins and losses by surface. 



*** =instructions
- Assume `djokovic`, `dplyr` and `ggplot2` are loaded.
- Create a factor for wins with labels `Win` and `Loss`.
- use a graphic appropriate for this type of variable to plot wins and losses by surface.

*** =hint
Use `geom_bar`.

*** =pre_exercise_code
```{r}
library(dplyr)
library(ggplot2)

djokovic <- read.csv("http://on-the-t.com/assets/data/djokovic2015.csv")
```



*** =solution
```{r}
djokovic <- djokovic %>%
	mutate(
		winner = factor(winner, labels = c("Loss", "Win"))
	)
	

djokovic %>% 
	ggplot(aes(x = surface, fill =  winner)) + 
	geom_bar()
```

*** =sct
```{r}
test_error()
success_msg("If you have a bar chart of wins and losses by surface, congrats!")
```




--- type:NormalExercise lang:r xp:200 skills:1 key:4ea0a4a1f1

##  Wrangling + Graphics

Plot the cumulative percentage of straight-set victories by match number Djokovic had in 2015. 



*** =instructions
- Assume `djokovic`, `dplyr` and `ggplot2` are loaded.
- Create a variable that indicates a straight set win. 
- Create a match number (from earliest to most recent). 
- Calculate the cumulative average of straight-set wins.
- Use a graphic appropriate for this type of variable to plot the cumulative straight-set sins by match number.


*** =hint
Use `geom_line`.

*** =pre_exercise_code
```{r}
library(dplyr)
library(ggplot2)

djokovic <- read.csv("http://on-the-t.com/assets/data/djokovic2015.csv")
```


*** =solution
```{r}
djokovic <- djokovic %>% 
	mutate(
		minsets = ifelse(grepl("SRB|Australian|Garros|Wimbledon|US Open", name), 3, 2), 
		sets = rowSums(!is.na(cbind(player1, player2, player3, player4, player5))),
		straightset = winner == 1 & minsets == sets,
		matchnum = length(name):1
	)

djokovic <- djokovic[order(djokovic$matchnum),]
djokovic$straightset_cummean <- cumsum(djokovic$straightset) / djokovic$matchnum	

djokovic %>%
	ggplot(aes(y = straightset_cummean * 100, x = matchnum)) + 
	geom_line(col = "#33CCFF") + 
	scale_y_continuous("Cumulative % Straight-Set Wins", lim = c(50, 100)) +
	scale_x_continuous("Matches Played")
```

*** =sct
```{r}
test_error()
success_msg("Did you get the plot the way you wanted? Great work!")
```


