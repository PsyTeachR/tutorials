# Detecting "runs" in a sequence

- Dale Barr (October 30, 2019)

Let's say you have a table like below, and you want to find the start and end frames where you have a run of `Z` amidst a, b, c, d.  Here is code that sets up this kind of situation. Don't worry if you don't understand this code; just run it to create the example data in `runsdata`, and have a look at that table.


```r
library("tidyverse")

create_run_vec <- function() {
  ## create a random string of letters with two runs
  c(rep(sample(letters[1:4]), sample(2:4, 4, TRUE)),
               rep("Z", 3),
               rep(sample(letters[1:4]), sample(2:4, 4, TRUE)),
               rep("Z", 3),
               rep(sample(letters[1:4], 2), sample(2:4, 2, TRUE)))
}

## 5 subjects, 3 trials each
runsdata <- tibble(
  subject = rep(1:5, each = 3),
  trial = rep(1:3, 5),
  stimulus = rerun(15, create_run_vec())) %>%
  unnest(stimulus) %>%
  group_by(subject, trial) %>%
  ungroup() %>%
  select(subject, trial, stimulus)
```

Let's say you want to find the start and stop frames where `Z` appears in `stimulus`, and do this independently for each combination of `subject` and `trial`.  Here's how `stimulus` looks for subject 1 and trial 1.


```
##  [1] "b" "b" "b" "a" "a" "a" "a" "c" "c" "d" "d" "d" "Z" "Z" "Z" "b" "b" "c" "c"
## [20] "c" "c" "d" "d" "d" "d" "a" "a" "a" "a" "Z" "Z" "Z" "a" "a" "d" "d" "d" "d"
```

So here you can see that the first run of Zs is from frame 13 to 15, 32 and the second is from 30 to 32. We want to write a function that processes the data for each trial and results in a table like this:

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> subject </th>
   <th style="text-align:right;"> trial </th>
   <th style="text-align:right;"> run </th>
   <th style="text-align:right;"> start_frame </th>
   <th style="text-align:right;"> end_frame </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:right;"> 32 </td>
  </tr>
</tbody>
</table>

</div>

The first thing to do is to add a logical vector to your tibble whose value is `TRUE` when the target value (e.g., `Z`) is present in the sequence, false otherwise.


```r
runsdata_tgt <- runsdata %>%
  mutate(is_target = (stimulus == "Z"))

head(runsdata_tgt)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> subject </th>
   <th style="text-align:right;"> trial </th>
   <th style="text-align:left;"> stimulus </th>
   <th style="text-align:left;"> is_target </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> b </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> b </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> b </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> a </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> a </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> a </td>
   <td style="text-align:left;"> FALSE </td>
  </tr>
</tbody>
</table>

</div>

We want to iterate over subjects and trials. We'll start by creating a tibble with columns `is_target` nested into a column called `subtbl`.


```r
runs_nest <- runsdata_tgt %>%
  select(-stimulus) %>% # don't need it anymore
  nest(subtbl = c(is_target))
```

We want to iterate over the little subtables stored within `subtbl` in each row of the table, passing the table to a function that will find the runs and return another table, which we'll store in new column. Let's write a function to detect the runs. That function will need the function `rle()` (Run-Length Encoding) from base R. We'll run that on the logical vector we created (`is_target`). Before creating the function, let's see what `rle()` does on the values in `is_target` for subject 1, trial 1.


```r
s1t1 <- runsdata_tgt %>% filter(subject == 1L, trial == 1L) %>% pull(is_target)

s1t1

rle(s1t1)
```

```
##  [1] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## [13]  TRUE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
## [25] FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE  TRUE FALSE FALSE FALSE FALSE
## [37] FALSE FALSE
## Run Length Encoding
##   lengths: int [1:5] 12 3 14 3 6
##   values : logi [1:5] FALSE TRUE FALSE TRUE FALSE
```

If that doesn't make sense, look at the help for `rle()` (type `?rle` in the console). Now we're ready to write our function, `detect_runs()`.


```r
detect_runs <- function(x) {  
  if (!is.logical(x[[1]])) stop("'x' must be a tibble whose first column is of type 'logical'")
  runs <- rle(x[[1]])
  run_start_fr <- c(1L, cumsum(runs$lengths[-length(runs$lengths)]) + 1L)
  run_end_fr <- run_start_fr + (runs$lengths - 1L)
  
  tgt_start <- run_start_fr[runs$values]
  tgt_end <- run_end_fr[runs$value]
  tibble(run = seq_along(tgt_start),
         start_fr = tgt_start,
         end_fr = tgt_end)
}
```

We can test the function on `s1t1` just to make sure it works.


```r
detect_runs(tibble(lvec = s1t1))
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> run </th>
   <th style="text-align:right;"> start_fr </th>
   <th style="text-align:right;"> end_fr </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:right;"> 32 </td>
  </tr>
</tbody>
</table>

</div>

OK, now we're ready to run the function.


```r
result <- runs_nest %>%
  mutate(runstbl = map(subtbl, detect_runs))

head(result)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> subject </th>
   <th style="text-align:right;"> trial </th>
   <th style="text-align:left;"> subtbl </th>
   <th style="text-align:left;"> runstbl </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE </td>
   <td style="text-align:left;"> 1, 2, 13, 30, 15, 32 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE </td>
   <td style="text-align:left;"> 1, 2, 13, 26, 15, 28 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE </td>
   <td style="text-align:left;"> 1, 2, 14, 27, 16, 29 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE </td>
   <td style="text-align:left;"> 1, 2, 11, 27, 13, 29 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE </td>
   <td style="text-align:left;"> 1, 2, 13, 29, 15, 31 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, TRUE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE, FALSE </td>
   <td style="text-align:left;"> 1, 2, 14, 28, 16, 30 </td>
  </tr>
</tbody>
</table>

</div>

Now we just have to unnest and we're done!


```r
data <- result %>%
  select(-subtbl) %>%
  unnest(runstbl)

head(data)
```

<div class="kable-table">

<table>
 <thead>
  <tr>
   <th style="text-align:right;"> subject </th>
   <th style="text-align:right;"> trial </th>
   <th style="text-align:right;"> run </th>
   <th style="text-align:right;"> start_fr </th>
   <th style="text-align:right;"> end_fr </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:right;"> 32 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:right;"> 28 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 16 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:right;"> 29 </td>
  </tr>
</tbody>
</table>

</div>


