Wrangling
================
Stoyan Dimitrov
November 19, 2019

# Data Import

## Importing Spreadsheets

Packages used are “readr” and “readxl”. Spreadsheets can be created in
text editors where new row is defined with a “return” and a column with
a predefined special character (, ; “space” “tab”). Often the first row
contains the column names. It is important to not if there is a header
or not before import a file.

## Paths and the Working Directory

Know the location of the file to be imported in R Studio and the working
directory of the R session. Check working directory  
`getwd()`

Change the working directory use:  
`setwd()`

Unless full path is provided R searches for files in the working
directory.

Get the full path of a file (extdata data file from the dslabs package
in this case):  
`system.file("extdata", package="dslabs")`

To list files in a certain folder:  
`list.files()`

Generate a full path to a
file

``` r
# The following **path** variable can be derived using **system.file()** function.
# The **file.path()** function generates a full path from a relative path and a file name. It also choses the correct slashes depending on the OS.

filename <- "any_data_file"
fullpath <- file.path(path, filename)
fullpath
```

Copy file from dslabs package to your working directory  
`file.copy(fullpath, getwd())`

Check if the file exists `file.exists(filename)`

## The readr and readxl Packages

readr a tidyverse library that includes functions reading data stored in
text file spreadsheets into
R.

| Function    | Format                                          | Typical Suffix |
| ----------- | ----------------------------------------------- | -------------- |
| read\_table | white space separated values                    | txt            |
| read\_csv   | comma separated values                          | csv            |
| read\_csv2  | semicolon separated values                      | csv            |
| read\_tsv   | tab delimited separated values                  | tsv            |
| read\_delim | general text file format, must define delimiter | txt            |

The readxl package provides functions to read in data in the MS Excel
format

| Function    | Format                | Typical Suffix |
| ----------- | --------------------- | -------------- |
| read\_excel | autodetect the format | xls, xlsx      |
| read\_xls   | original format       | xls            |
| read\_xlsx  | new format            | xlsx           |

The function `excel_sheets` gives us the names of the sheets in an Excel
file. These names can then be passed to the sheet argument in the three
functions above to access the desired the sheet.

`read_lines("name_of_the_file", n_max = 3)` reads the first few lines of
a file.

## Importing Data Using R-base Functions

`read.table` ; `read.csv`; `read.delim`

Differences with tidyverse:

  - creates a data frame instead of a tibble;

  - characters are converted to factors (this can be avoided by using
    read.csv(filename, **stringsAsFactors = FALSE**).

## Downloading files from the internet

Files can be read directly from the web instead from a local folder.  
`any_file_name <- read_csv("url")`

Download a file:  
`download.file("url", "any_file_name")`

Create a directory with a name that is (very likely to be) unique:  
`tempdir()`

Create a character string to name a file that is (very likely to be)
unique:  
`tempfile()`

Download a file, give a temp name load it into R and then erase it:

``` r
tmp_file <- tempfile()
download.file("url", tmp_filename)
dat <- read_csv(tmp_filename)
file.remove(tmp_filename)
```

Example: create a tibble in R called dat that contains the information
contained in the csv file stored on GitHub. Download the csv file to the
working directory and name the downloaded file
“MyData.csv”.

``` r
url <- "https://raw.githubusercontent.com/MyUserName/MyProject/master/MyData.csv "
dat <- read_csv(url)
download.file(url, "MyData.csv")
```

# Tidy Data

## Reshaping Data

### Tidy Data

Each row represents one observation and each column represents a
different variables that we have data on for those observations.

Differences between the wide and tidy formats

  - in wide format
      - each row includes several observations
      - one of the variables is stored in the header

To use the tidyverse it is necessary to wrangle the data into tidy
format.

### Reshaping Data

`library(tidyr)`

`gather()` converts wide data to tidy data. By default `gather()`
gathers all the columns, therefore use the third argument to specify the
columns. The first argument sets the name of the column that will hold
the variable that are currently kept in the wide data column names. The
second argument sets the column name for the column that will hold the
values in the column cells.

Example:

``` r
new_tidy_data <- wide_data %>%
  gather(year, fertility, `1960`:`2015`)

# or

new_tidy_data <- wide_data %>%
  gather(year, fertility, -country)

#`gather()` assumes that column names from the wide format are characters so they are converted to characters even if thery are integers. To avoid that it can be use `numeric()` or:

new_tidy_data <- wide_data %>%
  gather(year, fertility, -country, convert = TRUE)
```

`spread()` is the the inverse of gather. The first argument sets the
variable that will be used as the column names. The second argument
specifies which variables to use to fill out the cells.

### Separate and Unite

The following chunk saves the path to the “extdata” dir from the dslabs
package in the *path* variable, then it saves the full path to a data
file saved in the the “extdata” dir to *filename* variable

``` r
path <- system.file("extdata", package = "dslabs")
filename <- file.path(path, "life-expectancy-and-fertility-two-countries-example.csv")

raw_dat <- read_csv(filename)
select(raw_dat, 1:5)
```

`separate(data, c("coulmn_to_be_separated", "newly_separated_var",
"auxiliary_column_for_leftovers_of_the_name"), sep = "_", fill =
"right")` separates a column into multiple columns given a delimiting
character ("\_" by default, and it is not necessary to include it as in
the example). This is useful for example to separate column that has two
variables. The number of the additional columns is set by the names of
new columns given in the formula. If a column is to be separated into
two but it contains character strings with more than one of the
delimiting characters, the latter part of the variable will be lost
because there are no columns to contain it.

One approach is to add columns to contain the “leftovers” of the
character strings that are being separated. In that case the cells of
the additional columns that correspond to variables with less delimiting
characters will be filled with NA. And then pipe the result into
`unite()` function like this: `unite(var_name, newly_separated_var,
auxiliary_column_for_leftovers_of_the_name, sep = "_") A better approach
would be to use`separate(data, c(“column\_to\_be\_separated”,
“newly\_separated\_var”), sep = "\_“, extra =”merge")

Then `spread()` can be used on the column with second variable in order
to convert its values to new variables in the header and distribute the
values of the last column under those new variables.

# Joining tables

`library(tidyverse)` `library(dslabs)`

We want to explore the relationship between population size for US
states and electoral votes. We have population size in: `data(murders)`
`head(murders)`

And we have the electoral votes in: `data(polls_us_election_2016)`
`head(results_us_election_2016)`
