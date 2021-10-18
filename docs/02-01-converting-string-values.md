# Converting string values to numeric values for questionnaire scoring

- Dale Barr (August 18, 2021)

A very common situation is that you want to convert string values ("Almost Always", "Frequently") into numeric values (5, 4, etc) so that we can calculate a score.

The solution I present here will use something called a "lookup table" that matches string values to numbers.

First, let's assume the following (made-up) questionnaire asks four questions about hygiene. When you bathe, how often do you:

1. Wash your legs?
2. Wash behind your ears?
3. Wash between your toes?
4. Shampoo your hair?

Let's assume that answers are on a scale made up of the values "Never", "Sometimes", "Frequently", "Always", which we want to assign scores of 0, 1, 2, and 3 respectively.

Here is some (also made-up) data from five participants, stored in a tibble named `dat`.



<table>
 <thead>
  <tr>
   <th style="text-align:left;"> subj_id </th>
   <th style="text-align:left;"> wash_legs </th>
   <th style="text-align:left;"> wash_ears </th>
   <th style="text-align:left;"> wash_toes </th>
   <th style="text-align:left;"> shampoo </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> Sometimes </td>
   <td style="text-align:left;"> Never </td>
   <td style="text-align:left;"> Never </td>
   <td style="text-align:left;"> Frequently </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> Sometimes </td>
   <td style="text-align:left;"> Frequently </td>
   <td style="text-align:left;"> Frequently </td>
   <td style="text-align:left;"> Always </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> Never </td>
   <td style="text-align:left;"> Never </td>
   <td style="text-align:left;"> Never </td>
   <td style="text-align:left;"> Frequently </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S04 </td>
   <td style="text-align:left;"> Always </td>
   <td style="text-align:left;"> Always </td>
   <td style="text-align:left;"> Sometimes </td>
   <td style="text-align:left;"> Always </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S05 </td>
   <td style="text-align:left;"> Frequently </td>
   <td style="text-align:left;"> Sometimes </td>
   <td style="text-align:left;"> Never </td>
   <td style="text-align:left;"> Sometimes </td>
  </tr>
</tbody>
</table>

(If you want to make `dat` so you can follow along with the example by running the code in R, click the button below.)


<div class='webex-solution'><button>show me the R code to create the table 'dat'</button>



```r
library("tidyverse")

dat <- tribble(
  ~subj_id, ~wash_legs,   ~wash_ears,   ~wash_toes,   ~shampoo,
  "S01",    "Sometimes",  "Never",      "Never",      "Frequently",
  "S02",    "Sometimes",  "Frequently", "Frequently", "Always",
  "S03",    "Never",      "Never",      "Never",      "Frequently",
  "S04",    "Always",     "Always",     "Sometimes",  "Always",
  "S05",    "Frequently", "Sometimes",  "Never",      "Sometimes")
```


</div>


This data is in *wide* format: there is a single row for each participant with data for each question forming the columns. What we are going to do first is to convert this data from wide to long using `pivot_longer()`. (You'll see why in a minute.)


```r
dat_long <- dat %>%
  pivot_longer(cols = wash_legs:shampoo,
               names_to = "question", values_to = "response")
```

Take a look at `dat_long`.

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> subj_id </th>
   <th style="text-align:left;"> question </th>
   <th style="text-align:left;"> response </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> wash_legs </td>
   <td style="text-align:left;"> Sometimes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> wash_ears </td>
   <td style="text-align:left;"> Never </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> wash_toes </td>
   <td style="text-align:left;"> Never </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S01 </td>
   <td style="text-align:left;"> shampoo </td>
   <td style="text-align:left;"> Frequently </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> wash_legs </td>
   <td style="text-align:left;"> Sometimes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> wash_ears </td>
   <td style="text-align:left;"> Frequently </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> wash_toes </td>
   <td style="text-align:left;"> Frequently </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S02 </td>
   <td style="text-align:left;"> shampoo </td>
   <td style="text-align:left;"> Always </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> wash_legs </td>
   <td style="text-align:left;"> Never </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> wash_ears </td>
   <td style="text-align:left;"> Never </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> wash_toes </td>
   <td style="text-align:left;"> Never </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S03 </td>
   <td style="text-align:left;"> shampoo </td>
   <td style="text-align:left;"> Frequently </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S04 </td>
   <td style="text-align:left;"> wash_legs </td>
   <td style="text-align:left;"> Always </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S04 </td>
   <td style="text-align:left;"> wash_ears </td>
   <td style="text-align:left;"> Always </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S04 </td>
   <td style="text-align:left;"> wash_toes </td>
   <td style="text-align:left;"> Sometimes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S04 </td>
   <td style="text-align:left;"> shampoo </td>
   <td style="text-align:left;"> Always </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S05 </td>
   <td style="text-align:left;"> wash_legs </td>
   <td style="text-align:left;"> Frequently </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S05 </td>
   <td style="text-align:left;"> wash_ears </td>
   <td style="text-align:left;"> Sometimes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S05 </td>
   <td style="text-align:left;"> wash_toes </td>
   <td style="text-align:left;"> Never </td>
  </tr>
  <tr>
   <td style="text-align:left;"> S05 </td>
   <td style="text-align:left;"> shampoo </td>
   <td style="text-align:left;"> Sometimes </td>
  </tr>
</tbody>
</table>

Now we only have *one* variable we need to convert to numeric values (`response`) instead of the original four. There is an easy solution: we create a "lookup table" mapping the string values to the numeric values like so.

But first let's check what the unique string values are in the data. Our lookup table must match these *exactly* or the approach won't work.


```r
dat_long %>%
  distinct(response) %>%
  pull()
```

```
## [1] "Sometimes"  "Never"      "Frequently" "Always"
```
This step is important because sometimes these fields can have special characters that you don't normally see when you print out a table. The `distinct() %>% pull()` pattern will give you those values in a way that makes them visible. For instance, a student once had values in the data like this:

```
[1] "Somewhat\nInfrequently" "Somewhat\nFrequently"   "Very\nInfrequently"
[4] "Almost\nNever"          "Very\nFrequently"       "Almost\nAlways"
```
and the lookup table was constantly failing because the lookup table they created did not have the `\n` in the middle of each string. *Computers are very literal!*

OK now we're ready to create our lookup table where we match these four values to numbers.


```r
lookup <- tribble(
  ~response, ~score,
  "Never",      0,
  "Sometimes",  1,
  "Frequently", 2,
  "always",     3)
```

As the final step, we `inner_join()` the original table `dat_long` to `lookup` on the variable `response`.


```r
joined <- inner_join(dat_long, lookup, by = "response")
```
**IMPORTANT**: You should check to make sure that the join worked as intended. The values in the lookup table must *exactly* match the values in the `response` column of `dat_long`. It is easy to make a typo in the lookup table, and those values will be lost. An easy test is to make sure that the number of rows in `joined` matches the number of rows in `dat_long`.


```r
stopifnot(nrow(joined) == nrow(dat_long))
```

The function `stopifnot()` will make our script fail if the stated condition (that both tables have the same number of rows) is not satisfied.

Uh oh. Running it gives `Error: nrow(joined) == nrow(dat_long) is not TRUE`. The test failed, because I deliberately included a typo in the lookup table. Can you see it?


<div class='webex-solution'><button>what is it?</button>


`always` should be `Always`. Capitalization matters!


</div>


So let's fix our lookup table and then we should be good to go. Here is the full code for this demonstration:


```r
dat_long <- dat %>%
  pivot_longer(cols = wash_legs:shampoo,
               names_to = "question", values_to = "response")

## check for hidden values
dat_long %>%
  distinct(response) %>%
  pull()

lookup <- tribble(
  ~response, ~score,
  "Never",      0,
  "Sometimes",  1,
  "Frequently", 2,
  "Always",     3)

joined <- inner_join(dat_long, lookup, by = "response")

## test whether the number of rows match
stopifnot(nrow(joined) == nrow(dat_long))
```
You could then use 


```r
joined %>%
  group_by(subj_id) %>%
  summarise(hygiene = sum(score))
```
to calculate a score for each subject.
