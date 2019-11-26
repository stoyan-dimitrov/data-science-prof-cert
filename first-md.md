My First Markdown
================
Stoyan Dimitrov
November 19, 2019

Wrangling
=========

Data Import
-----------

### Importing Spreadsheets

Packages used are "readr" and "readxl". Spreadhseets can be created in text editors where nwe row is defined with a "return" and a column with a predefined special character (, ; "space" "tab"). Often the first row contains the column names. It is important to not if there is a header or not before import a file. \#\#\#Paths and the Working Directory 1. Know the location of the file to be imported in RStudio and the working directory of the R session.

See working directory
`getwd()`

Change the working directory use:
`setwd()`

Unless full path is provided R searches for files in the working directory.

Get the full path of a file (extdata data file from the dslabs package in this case):
`system.file("extdata", package="dslabs")`

To list files in a certain folder:
`list.files()`

Generate a full path to a file

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

### The readr and readxl Packages

readr a tidyverse library that includes funcitons reading data stored in text file spreadsheets into R.

| Function    | Format                                          | Typical Suffix |
|-------------|-------------------------------------------------|----------------|
| read\_table | white space separated values                    | txt            |
| read\_csv   | comma separated values                          | csv            |
| read\_csv2  | semicolon separated values                      | csv            |
| read\_tsv   | tab delimited separated values                  | tsv            |
| read\_delim | general text file format, must define delimiter | txt            |

The readxl package provides functions to read in data in the MS Excel format

| Function    | Format                | Typical Suffix |
|-------------|-----------------------|----------------|
| read\_excel | autodetect the format | xls, xlsx      |
| read\_xls   | original format       | xls            |
| read\_xlsx  | new format            | xlsx           |

The function `excel_sheets` gives us the names of the sheets in an Excel file. These names can then be passed to the sheet argument in the three functions above to access the desired the sheet.

`read_lines("name_of_the_file", n_max = 3)` reads the first few lines of a file.

### Importing Data Using R-base Functions

`read.table` ; `read.csv`; `read.delim`

Differences with tidyverse:

-   creates a data frame instead of a tibble;

-   characters are converted to factors (this can be avoided by using read.csv(filemane, **stringsAsFactors = FALSE**).

### Downloading files from the internet

Files can be read directly from the web instead from a local folder.
`any_file_name <- read_csv("url")`

Download a flie:
`download.file("url", "any_file_name")`

Create a directory with a name that is (very likely to be) unique:
`tempdir()`

Create a character string to name a file that is (very likely to be) unique:
`tempfile()`

Download a file, give a temp name load it into R and then erase it:

``` r
tmp_file <- tempfile()
download.file("url", tmp_filename)
dat <- read_csv(tmp_filename)
file.remove(tmp_filename)
```

Example: create a tibble in R called dat that contains the information contained in the csv file stored on Github. Download the csv file to the working directory and name the downloaded file “MyData.csv”.

``` r
url <- "https://raw.githubusercontent.com/MyUserName/MyProject/master/MyData.csv "
dat <- read_csv(url)
download.file(url, "MyData.csv")
```

Tidy Data
---------

### Reshaping Data

#### Tidy Data

Each row represents one observation and each column represents a different variables that we have data on for those observations.

Differences between the wide and tidy formants

-   in wide format
    -   each row includes several observations
    -   one of the variables is stored in the header

To use the tidyverse it is necesarry to wrangle the data into tidy format.
