---
title       : Machine Learning
description : Exercises to Test Concepts in Part 4 of Data Science Workshop


--- type:NormalExercise lang:r xp:100 skills:1 key:99f5dc3cd37ec9a6b55f72608d8a0ae79735f68a
## Preparing Data

Before conducting any machine learning we often have to do some pre-processing of our data.


*** =instructions
- Import the `match_stats` data 
- Obtain an indicator for all numerical variables except `winner` (our outcome)
- Obtain an imputed dataset
- Scale variables to have zero mean and standard deviation of 1


*** =hint
Use `mice` and `scale`.

*** =pre_exercise_code
```{r}
library(mice)
library(dplyr)

match_stats <- read.csv(file = "http://on-the-t.com/assets/data/match_stats.csv")
match_stats <- match_stats[5000:nrow(match_stats),]
```

*** =solution
```{r}
numerical <- (match_stats %>% summarise_each(funs(class)) %in% c("integer", "numeric")) & names(match_stats) != "winner"

imputed <- mice(match_stats[,numerical], m = 1)

match_stats_complete <- mice::complete(imputed, 1) 

# Replace imputed and scale
match_stats[, numerical] <- scale(match_stats_complete)
```

*** =sct
```{r}
test_error()
success_msg("Fantastic work!")
```


--- type:NormalExercise lang:r xp:100 skills:1 key:bf4ff8dc0a

##  Decision Tree

Create a decision tree for wins using all other numeric variables.

*** =instructions
- Assume the imputed and scaled `match_stats` are available
- Fit a decision tree 
- Save your results to the object `fit`


*** =hint
Use `rpart`

*** =pre_exercise_code
```{r}
library(dplyr)
library(rpart)

match_stats <- read.csv("http://on-the-t.com/assets/data/match_stats_prepped.csv")
```



*** =solution
```{r}
vars <- (match_stats %>% summarise_each(funs(class)) %in% c("integer", "numeric")) 

formula <- winner ~ .

fit <- rpart(formula, data = match_stats[, vars], method = "class")

fit
```

*** =sct
```{r}
test_object("fit")
test_error()
success_msg("Excellent job!")
```



--- type:NormalExercise lang:r xp:100 skills:1 key:895c70acb4

##  Random Forest

Use the ensemble method, random forest, for modelling wins.

*** =instructions
- Assume the imputed and scaled `match_stats` are available
- Fit a random forest with `ntree = 500`
- Summarise and find the most important variable
- Save the importance as `import`


*** =hint
Use `randomForest`


*** =pre_exercise_code
```{r}
library(randomForest)
library(dplyr)

match_stats <- read.csv(file = "http://on-the-t.com/assets/data/match_stats_prepped.csv")
```

*** =solution
```{r}
vars <- (match_stats %>% summarise_each(funs(class)) %in% c("integer", "numeric")) 

formula <- factor(winner) ~ .

fit <- randomForest(formula, data = match_stats[,vars], ntree = 500)

summary(fit)

import <- importance(fit)

fit
```

*** =sct
```{r}
test_object("import")
test_error()
success_msg("You are really getting this!")
```



--- type:NormalExercise lang:r xp:100 skills:1 key:f4de900ee9

##  Principal Components

Conduct a PCA on the `diff` variables.

*** =instructions
- Assume the imputed and scaled `match_stats` are available
- Perform a PCA
- Determine the explained variance of the first two components
- Save this proportion as the object `explained2`


*** =hint
Use `prcomp`


*** =pre_exercise_code
```{r}
match_stats <- read.csv(file = "http://on-the-t.com/assets/data/match_stats_prepped.csv")
```

*** =solution
```{r}
vars <- grep("diff", names(match_stats), val = TRUE)

pca <- prcomp(match_stats[,vars], scale = TRUE)

explained2 <-  sum(pca$sdev[1:2] ^ 2) / sum(pca$sdev ^ 2)

explained2
```

*** =sct
```{r}
test_object("explained2")
test_error()
success_msg("Fabulous!")
```

--- type:NormalExercise lang:r xp:100 skills:1 key:0569910546

##  Hierarchical Clustering

Use hierarchical clustering to find 5 clusters among the `diff` variables.

*** =instructions
- Assume the imputed and scaled `match_stats` are available
- Perform hierarchical clustering with euclidean distance and complete linkage method
- Obtain the cluster assignments for 5-group clustering
- Save this group vector as the object `cluster`


*** =hint
Use `hclust`


*** =pre_exercise_code
```{r}
match_stats <- read.csv(file = "http://on-the-t.com/assets/data/match_stats_prepped.csv")
```

*** =solution
```{r}
vars <- grep("diff", names(match_stats), val = TRUE)

d <- dist(match_stats[,vars], method = "euclidean")

tree <- hclust(d, method = "complete")

clusters <- cutree(tree, k = 5)

table(clusters)
```

*** =sct
```{r}
test_object("clusters")
test_error()
success_msg("You've done it! Congratulations!")
```


