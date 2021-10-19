# Multi-Row Headers

- Lisa DeBruine (2021-10-17)



```r
# required packages
library(tidyverse)
library(readxl)
```

A student on our help forum once asked for help making a wide-format dataset long. When I tried to load the data, I realised the first three rows were all header rows. Here's the code I wrote to deal with it.

First, I'll make a small CSV "file" below. In a typical case, you'd read the data in from a file.


```r
demo_csv <- "SUB1, SUB1, SUB1, SUB1, SUB2, SUB2, SUB2, SUB2
COND1, COND1, COND2, COND2, COND1, COND1, COND2, COND2
X, Y, X, Y, X, Y, X, Y
10, 15, 6, 2, 42, 4, 32, 5
4, 43, 7, 34, 56, 43, 2, 33
77, 12, 14, 75, 36, 85, 3, 2"
```

If you try to read in this data, you'll get a message about the duplicate column names and the resulting table will have "fixed" column headers and the next two columns headers as the first two rows.


```r
data <- read_csv(I(demo_csv))
```

<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> SUB1...1 </th>
   <th style="text-align:left;"> SUB1...2 </th>
   <th style="text-align:left;"> SUB1...3 </th>
   <th style="text-align:left;"> SUB1...4 </th>
   <th style="text-align:left;"> SUB2...5 </th>
   <th style="text-align:left;"> SUB2...6 </th>
   <th style="text-align:left;"> SUB2...7 </th>
   <th style="text-align:left;"> SUB2...8 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> COND2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 15 </td>
   <td style="text-align:left;"> 6 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 42 </td>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 32 </td>
   <td style="text-align:left;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 4 </td>
   <td style="text-align:left;"> 43 </td>
   <td style="text-align:left;"> 7 </td>
   <td style="text-align:left;"> 34 </td>
   <td style="text-align:left;"> 56 </td>
   <td style="text-align:left;"> 43 </td>
   <td style="text-align:left;"> 2 </td>
   <td style="text-align:left;"> 33 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 77 </td>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 14 </td>
   <td style="text-align:left;"> 75 </td>
   <td style="text-align:left;"> 36 </td>
   <td style="text-align:left;"> 85 </td>
   <td style="text-align:left;"> 3 </td>
   <td style="text-align:left;"> 2 </td>
  </tr>
</tbody>
</table>

Instead, you should read in just the header rows by setting `n_max` equal to the number of header rows and `col_names` to `FALSE`.


```r
data_head <- read_csv(demo_csv, 
                      n_max = 3, 
                      col_names = FALSE)
```

You will get a table that looks like this:

<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> X1 </th>
   <th style="text-align:left;"> X2 </th>
   <th style="text-align:left;"> X3 </th>
   <th style="text-align:left;"> X4 </th>
   <th style="text-align:left;"> X5 </th>
   <th style="text-align:left;"> X6 </th>
   <th style="text-align:left;"> X7 </th>
   <th style="text-align:left;"> X8 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> SUB2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> COND2 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
  </tr>
</tbody>
</table>

You can then make new header names by pasting together the names in the three rows by summarising across all the columns with the `paste()` function and collapsing them using "_". Use `unlist()` and `unname()` to convert the result from a table to a vector.


```r
new_names <- data_head %>%
  summarise(across(.fns = paste, collapse = "_")) %>%
  unlist() %>% unname()

new_names
```

```
## [1] "SUB1_COND1_X" "SUB1_COND1_Y" "SUB1_COND2_X" "SUB1_COND2_Y" "SUB2_COND1_X"
## [6] "SUB2_COND1_Y" "SUB2_COND2_X" "SUB2_COND2_Y"
```

Now you can read in the data without the three header rows. Use `skip` to skip the headers and set `col_names` to the new names.


```r
data <- read_csv(demo_csv, 
                 skip = 3, 
                 col_names = new_names,
                 show_col_types = FALSE)
```


<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> SUB1_COND1_X </th>
   <th style="text-align:right;"> SUB1_COND1_Y </th>
   <th style="text-align:right;"> SUB1_COND2_X </th>
   <th style="text-align:right;"> SUB1_COND2_Y </th>
   <th style="text-align:right;"> SUB2_COND1_X </th>
   <th style="text-align:right;"> SUB2_COND1_Y </th>
   <th style="text-align:right;"> SUB2_COND2_X </th>
   <th style="text-align:right;"> SUB2_COND2_Y </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 15 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 42 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 32 </td>
   <td style="text-align:right;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 43 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:right;"> 56 </td>
   <td style="text-align:right;"> 43 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 33 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 77 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 75 </td>
   <td style="text-align:right;"> 36 </td>
   <td style="text-align:right;"> 85 </td>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:right;"> 2 </td>
  </tr>
</tbody>
</table>

If you have an excel file that merges the duplicate headers across rows, it's a little trickier, but still do-able. 

![](images/multirow-excel.png)

The first steps is the same: read in the first three rows.


```r
data_head <- read_excel("data/3headers_demo.xlsx",
                        n_max = 3, 
                        col_names = FALSE)
```

<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> ...1 </th>
   <th style="text-align:left;"> ...2 </th>
   <th style="text-align:left;"> ...3 </th>
   <th style="text-align:left;"> ...4 </th>
   <th style="text-align:left;"> ...5 </th>
   <th style="text-align:left;"> ...6 </th>
   <th style="text-align:left;"> ...7 </th>
   <th style="text-align:left;"> ...8 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:left;"> NA </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:left;"> NA </td>
  </tr>
  <tr>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
   <td style="text-align:left;"> X </td>
   <td style="text-align:left;"> Y </td>
  </tr>
</tbody>
</table>

The code below starts at the second column and fills in any missing data with the value in the previous column.


```r
for (i in 2:ncol(data_head)) {
  prev <- data_head[, i-1]
  this <- data_head[, i]
  missing <- is.na(this)
  this[missing, ] <- prev[missing, ]
  data_head[, i] <- this
}
```

Now you can continue generating the pasted name the same as above.


```r
new_names <- data_head %>%
  summarise(across(.fns = paste, collapse = "_")) %>%
  unlist() %>% unname()

new_names
```

```
## [1] "SUB1_COND1_X" "SUB1_COND1_Y" "SUB1_COND2_X" "SUB1_COND2_Y" "SUB2_COND1_X"
## [6] "SUB2_COND1_Y" "SUB2_COND2_X" "SUB2_COND2_Y"
```

If your data are set up with multiple headers, you'll probably want to change the shape of the data. Here's a quick example how to use `pivot_longer()` and `pivot_wider()` to do this with variable names like above.


```r
data <- read_excel("data/3headers_demo.xlsx", 
                   skip = 3, 
                   col_names = new_names)

data_long <- data %>%
  # add a row ID column if one doesn't exist already
  mutate(trial_id = row_number()) %>%
  # make a row for each data column
  pivot_longer(
    cols = -trial_id, # everything except trial_id
    names_to = c("sub_id", "condition", "coord"),
    names_sep = "_",
    values_to = "val"
  ) %>%
  # make x and y coord columns
  pivot_wider(
    names_from = coord,
    values_from = val
  )
```

<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> trial_id </th>
   <th style="text-align:left;"> sub_id </th>
   <th style="text-align:left;"> condition </th>
   <th style="text-align:right;"> X </th>
   <th style="text-align:right;"> Y </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.8316380 </td>
   <td style="text-align:right;"> 0.7881552 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.3941482 </td>
   <td style="text-align:right;"> 0.2056488 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.9332829 </td>
   <td style="text-align:right;"> 0.1530898 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.6189847 </td>
   <td style="text-align:right;"> 0.9400281 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.4147148 </td>
   <td style="text-align:right;"> 0.1366791 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.9805130 </td>
   <td style="text-align:right;"> 0.7493469 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.1048907 </td>
   <td style="text-align:right;"> 0.6573472 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.9579583 </td>
   <td style="text-align:right;"> 0.3430333 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.5577673 </td>
   <td style="text-align:right;"> 0.0956297 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.3045316 </td>
   <td style="text-align:right;"> 0.3540656 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.3621907 </td>
   <td style="text-align:right;"> 0.8460132 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.0167339 </td>
   <td style="text-align:right;"> 0.1886913 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.4326746 </td>
   <td style="text-align:right;"> 0.8276863 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.2845026 </td>
   <td style="text-align:right;"> 0.6236266 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.0439374 </td>
   <td style="text-align:right;"> 0.5379287 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.0712748 </td>
   <td style="text-align:right;"> 0.3511542 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.6545546 </td>
   <td style="text-align:right;"> 0.6501679 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.9202481 </td>
   <td style="text-align:right;"> 0.2525272 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.8117072 </td>
   <td style="text-align:right;"> 0.3455603 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.7073851 </td>
   <td style="text-align:right;"> 0.4249118 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.0679236 </td>
   <td style="text-align:right;"> 0.6978207 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> SUB1 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.3979061 </td>
   <td style="text-align:right;"> 0.6922928 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND1 </td>
   <td style="text-align:right;"> 0.5282960 </td>
   <td style="text-align:right;"> 0.1093352 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> SUB2 </td>
   <td style="text-align:left;"> COND2 </td>
   <td style="text-align:right;"> 0.6622162 </td>
   <td style="text-align:right;"> 0.5567239 </td>
  </tr>
</tbody>
</table>

