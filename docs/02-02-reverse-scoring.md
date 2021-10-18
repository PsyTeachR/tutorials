# How to reverse score questionnaire items

- Dale Barr (January 2021)

Sometimes it's necessary to reverse the scores given to questionnaire items. For instance, if your scale is the Dog Appreciation Scale (DAS) [I just made this up] you might have some items where 'strongly agree' is associated with appreciating dogs (and should be given the highest score) while other items where it is associated with loathing them (and should be given the lowest).




Let's say that the DAS scale has six items measuring dog appreciation. People respond to each of the six items on a 5 point likert scale, where 1=strongly disagree, 2=somewhat disagree, 3=neutral, 4=somewhat agree, 5=strongly agree.

<table>
<caption>(\#tab:dog-scale)The Dog Appreciation Scale</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> item </th>
   <th style="text-align:left;"> reverse_score </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> I like dogs </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dogs are fun </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cats are better than dogs </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dogs are helpful </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dogs are too noisy </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dogs are too much responsibility </td>
   <td style="text-align:left;"> TRUE </td>
  </tr>
</tbody>
</table>

Here is some made-up questionnaire data with 6 items and 3 subjects, contained in the tibble named `das`. We want to reverse score the items "Cats are better than dogs", "Dogs are too noisy", and "Dogs are too much responsibility" before summing up the total for each subject.

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> subj_id </th>
   <th style="text-align:left;"> item </th>
   <th style="text-align:right;"> score </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> I like dogs </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> Dogs are fun </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> Cats are better than dogs </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> Dogs are helpful </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> Dogs are too noisy </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> Dogs are too much responsibility </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> I like dogs </td>
   <td style="text-align:right;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> Dogs are fun </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> Cats are better than dogs </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> Dogs are helpful </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> Dogs are too noisy </td>
   <td style="text-align:right;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> Dogs are too much responsibility </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> I like dogs </td>
   <td style="text-align:right;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> Dogs are fun </td>
   <td style="text-align:right;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> Cats are better than dogs </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> Dogs are helpful </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> Dogs are too noisy </td>
   <td style="text-align:right;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> Dogs are too much responsibility </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
</tbody>
</table>

</div>


<div class='webex-solution'><button>show me the code to create the table 'das'</button>



```r
library("tidyverse")

das <- tribble(
  ~subj_id, ~item, ~score,
  "S01", "I like dogs",                      5,
  "S01", "Dogs are fun",                     5,
  "S01", "Cats are better than dogs",        1,
  "S01", "Dogs are helpful",                 4,
  "S01", "Dogs are too noisy",               2,
  "S01", "Dogs are too much responsibility", 2,
  "S02", "I like dogs",                      3,
  "S02", "Dogs are fun",                     4,
  "S02", "Cats are better than dogs",        2,
  "S02", "Dogs are helpful",                 4,
  "S02", "Dogs are too noisy",               3,
  "S02", "Dogs are too much responsibility", 5,
  "S03", "I like dogs",                      1,
  "S03", "Dogs are fun",                     3,
  "S03", "Cats are better than dogs",        5,
  "S03", "Dogs are helpful",                 2,
  "S03", "Dogs are too noisy",               4,
  "S03", "Dogs are too much responsibility", 5)
```


</div>


First, we assume that you have your data in long format, like the table above. If you don't, then please see the materials on reshaping from wide to long, such as [this section from the MSC book](https://psyteachr.github.io/msc-data-skills/tidyr.html#pivot_longer).

We are going to use a programming trick that we'll call "the N-plus-one-minus-X trick" to score the items that need to be reverse coded. This trick will work whenever you have a scale with N scale points that goes in integer steps from 1 to N (e.g., 1, 2, 3, 4, 5). You subtract Xs (each observed score) from N+1 to get the reversed value.

*newscore = (number_of_scale_points + 1) - oldscore*

So if you have a 5 point scale, it is:

*newscore = 6 - oldscore*

and a 7 point scale is

*newscore = 8 - oldscore*.

You can see this works using the following code:


```r
oldscores <- 1:5
newscores <- 6 - oldscores

rbind(oldscores, newscores)
```

```
##           [,1] [,2] [,3] [,4] [,5]
## oldscores    1    2    3    4    5
## newscores    5    4    3    2    1
```

**Note: If your scale goes from 0 to N, then use N - X rather than (N + 1) - X to reverse score.**

So we can see already that we need something like:


```r
das %>%
  mutate(newscore = 6 - score)
```

but *only* for those items that need to be reverse scored. This is where `if_else()` comes in. Or, better said, where `if_else()` comes `%in%` (if you can pardon a bit of R humor).


```r
das_coded <- das %>%
  mutate(newscore = if_else(item %in% c("Cats are better than dogs",
                                        "Dogs are too noisy",
                                        "Dogs are too much responsibilitiy"),
                            6 - score,
                            score))
```

The code above adds a new variable `newscore` which is the result of the `if_else()` command and stores the resulting table in `das_coded`. This command has the following syntax:

`if_else(condition, value_if_true, value_if_false)`.

So, if the current value of item is found within the vector of options (that's what the `%in%` operator does), the first expression evaluates to `TRUE`, and `6-score` is returned; if the first expression evaluates to `FALSE`, then `score` is returned.

But whenever you recode or score a variable, you should *ALWAYS* check that your code is correct, because typos are likely. The best way to do this is to run a little test in the console. You can just print out the data from `das_coded`, or if you have a lot of data, use `distinct()` to look at check the distinct values observed in the data.


```r
das_coded %>%
  distinct(item, score, newscore) %>%
  print(n = +Inf) ## this makes sure *all* rows are printed, not just the first 20
```

```
## # A tibble: 16 × 3
##    item                             score newscore
##    <chr>                            <dbl>    <dbl>
##  1 I like dogs                          5        5
##  2 Dogs are fun                         5        5
##  3 Cats are better than dogs            1        5
##  4 Dogs are helpful                     4        4
##  5 Dogs are too noisy                   2        4
##  6 Dogs are too much responsibility     2        2
##  7 I like dogs                          3        3
##  8 Dogs are fun                         4        4
##  9 Cats are better than dogs            2        4
## 10 Dogs are too noisy                   3        3
## 11 Dogs are too much responsibility     5        5
## 12 I like dogs                          1        1
## 13 Dogs are fun                         3        3
## 14 Cats are better than dogs            5        1
## 15 Dogs are helpful                     2        2
## 16 Dogs are too noisy                   4        2
```

Here we can see that "Cats are better than dogs" and "Dogs are too noisy" have been successfully reverse scored. We can also see that the items that should be forward scored, e.g., "I like dogs", are indeed forward scored (the scores don't change).

But our reverse scoring of "Dogs are too much responsibility" has failed. Can you see the problem in our code (hint: typo).


<div class='webex-solution'><button>no, I can't see the problem</button>


`responsibility` is mistyped as `responsibilitiy`


</div>


So the correct code is:


```r
das_coded <- das %>%
  mutate(newscore = if_else(item %in% c("Cats are better than dogs",
                                        "Dogs are too noisy",
                                        "Dogs are too much responsibility"),
                            6 - score,
                            score))
```


```r
das_coded %>%
  distinct(item, score, newscore) %>%
  print(n = +Inf) ## this makes sure *all* rows are printed, not just the first 20
```

```
## # A tibble: 16 × 3
##    item                             score newscore
##    <chr>                            <dbl>    <dbl>
##  1 I like dogs                          5        5
##  2 Dogs are fun                         5        5
##  3 Cats are better than dogs            1        5
##  4 Dogs are helpful                     4        4
##  5 Dogs are too noisy                   2        4
##  6 Dogs are too much responsibility     2        4
##  7 I like dogs                          3        3
##  8 Dogs are fun                         4        4
##  9 Cats are better than dogs            2        4
## 10 Dogs are too noisy                   3        3
## 11 Dogs are too much responsibility     5        1
## 12 I like dogs                          1        1
## 13 Dogs are fun                         3        3
## 14 Cats are better than dogs            5        1
## 15 Dogs are helpful                     2        2
## 16 Dogs are too noisy                   4        2
```

We've done it! Now we can proceed to analyze our data further.
