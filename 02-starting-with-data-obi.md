---
layout: topic
title: Starting with data
author: Data Carpentry contributors
minutes: 20
---

------------

> ## Learning Objectives
>
> * load external data (CSV files) in memory using the survey table
>  (`surveys.csv`) as an example
> * explore the structure and the content of the data in R
> * understand what are factors and how to manipulate them

------------

# Presentation of the Survey Data


We are studying the species and weight of animals caught in plots in our study
area. The dataset is stored as a `csv` file: each row holds information for a
single animal, and the columns represent:

| Column           | Description                        |
|------------------|------------------------------------|
| record\_id       | Unique id for the observation      |
| month            | month of observation               |
| day              | day of observation                 |
| year             | year of observation                |
| plot\_id         | ID of a particular plot            |
| species\_id      | 2-letter code                      |
| sex              | sex of animal ("M", "F")           |
| hindfoot\_length | length of the hindfoot in mm       |
| weight           | weight of the animal in grams      |
| genus            | genus of animal                    |
| species          | species of animal                  |
| taxa             | e.g. Rodent, Reptile, Bird, Rabbit |
| plot\_type       | type of plot                       |

We are going to use the R function `download.file()` to download the CSV file
that contains the survey data from figshare, and we will use `read.csv()` to
load into memory (as a `data.frame`) the content of the CSV file:

- First, make sure you are in the correct working directory by typing `getwd()`.
- Second, create a new directory within this working directory called `data`. You
can do this by clicking on the new folder icon in RStudio under the file tab, or
by typing `dir.create("data")` at the terminal.
- Third, download the data:

```
>getwd()
>dir.create("data")
>download.file("http://files.figshare.com/2236372/combined.csv", "data/my_survey_data.csv")
```

#### Importing data with `read.csv`

You should be able to see the ```my_survey_data.csv``` in the data folder  are now ready to load the data:

```
>surveys <- read.csv('data/my_survey_data.csv')
```

This statement doesn't produce any output because assignment doesn't display
anything. If we want to check that our data has been loaded, we can print the
variable's value: `surveys`

Alternatively, wrapping an assignment in parentheses will perform the assignment
and display it at the same time.

```
>(surveys <- read.csv('data/my_survey_data.csv'))
```

Wow... that was a lot of output. At least it means the data loaded
properly. Let's check the top (the first 6 lines) of this `data.frame` using the
function `head()`:

```
head(surveys)
```


Let's now check the **structure** of this `data.frame` in more details with the
function `str()`:

```
>str(surveys)
```
#### Importing with `read.table`

Some times data are in a format different from csv

### Challenge

Based on the output of `str(surveys)`, can you answer the following questions?

* What is the class of the object `surveys`?
* How many rows and how many columns are in this object?
* How many species have been recorded during these surveys?


As you can see, the columns `species` and `sex` are of a special class called
`factor`. Before we learn more about the `data.frame` class, we are going to
talk about factors. They are very useful but not necessarily intuitive, and
therefore require some attention.


## Factors


Factors are used to represent categorical data. Factors can be ordered or
unordered and are an important class for statistical analysis and for plotting.

Factors are stored as integers, and have labels associated with these unique
integers. While factors look (and often behave) like character vectors, they are
actually integers under the hood, and you need to be careful when treating them
like strings.

Once created, factors can only contain a pre-defined set values, known as
*levels*. By default, R always sorts *levels* in alphabetical order. For
instance, if you have a factor with 2 levels:

```
>sex <- factor(c("male", "female", "female", "male"))
```

R will assign `1` to the level `"female"` and `2` to the level `"male"` (because
`f` comes before `m`, even though the first element in this vector is
`"male"`). You can check this by using the function `levels()`, and check the
number of levels using `nlevels()`:

```
>levels(sex)
>nlevels(sex)
```

Sometimes, the order of the factors does not matter, other times you might want
to specify the order because it is meaningful (e.g., "low", "medium", "high") or
it is required by particular type of analysis. Additionally, specifying the
order of the levels allows us to compare levels:

```
>food <- factor(c("low", "high", "medium", "high", "low", "medium", "high"))
>levels(food)
>
>food <- factor(food, levels=c("low", "medium", "high"))
>levels(food)
>
>min(food) ## doesn't work
>
>food <- factor(food, levels=c("low", "medium", "high"), ordered=TRUE)
>levels(food)
>min(food) ## works!
```

In R's memory, these factors are represented by numbers (1, 2, 3). They are
better than using simple integer labels because factors are self describing:
`"low"`, `"medium"`, `"high"`" is more descriptive than `1`, `2`, `3`. Which
is low?  You wouldn't be able to tell with just integer data. Factors have this
information built in. It is particularly helpful when there are many levels
(like the species in our example data set).

### Converting factors

If you need to convert a factor to a character vector, simply use
`as.character(x)`.

Converting a factor to a numeric vector is however a little trickier, and you
have to go via a character vector. Compare:

```
f <- factor(c(1, 5, 10, 2))
as.numeric(f)               ## wrong! and there is no warning...
as.numeric(as.character(f)) ## works...
as.numeric(levels(f))[f]    ## The recommended way.
```

### Challenge

The function `table()` tabulates observations and can be used to create
bar plots quickly. For instance:

```
exprmt <- factor(c("treat1", "treat2", "treat1", "treat3", "treat1", "control","treat1", "treat2", "treat3"))
table(exprmt)
barplot(table(exprmt))
```

How can you recreate this plot but by having "control" being listed
last instead of first?

<!---
```{r correct-order, purl=FALSE}
exprmt <- factor(exprmt, levels=c("treat1", "treat2", "treat3", "control"))
barplot(table(exprmt))
```
--->
