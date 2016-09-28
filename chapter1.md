---
title       : Exploratory Data Analysis
description : Exercises to Test Concepts in Part 1 of Data Science Workshop



--- type:NormalExercise lang:r xp:100 skills:1 key:99f5dc3cd37ec9a6b55f72608d8a0ae79735f68a
## Importing

A CSV of the 2015 activity of Novak Djokovic is stored on the \code{on-the-t.com} website. This exercise tests your ability to read-in the file.


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


