# tidyethnicnews

An R Package for Turning Ethnic NewsWatch Search Results into Tidyverse-ready Dataframes

Author: [Jae Yeon Kim](https://jaeyk.github.io/)

File an [issue](https://github.com/jaeyk/tidyethnicnews/issues) if you have problems, questions or suggestions.

## Summary

The [Ethnic NewsWatch database](https://about.proquest.com/products-services/ethnic_newswatch.html), provided by [ProQuest](https://about.proquest.com/), has compiled more than 2.5 million articles published in US ethnic newspapers and magazines. The database covers the following groups:

- African American/Caribbean/African
- Arab/Middle Eastern
- Asian/Pacific Islander
- European/Eastern European
- Latinx
- Jewish
- Native Americans

This data source helps researchers investigate the political voice of minority groups in the US with rich and extensive text data. `tidyethnicnews` provides functions to turn Ethnic NewsWatch search results into tidyverse-ready dataframes.

## Installation

```r

## Install the current development version from GitHub

devtools::install_github("jaeyk/tidyethnicnews",
                         dependencies = TRUE)
```

## Responsible use
`tidyethnicnews` should be used in strict accordance with Ethnic NewsWatch's [Terms of Use](https://about.proquest.com/about/terms-and-conditions.html). ProQuest does not allow web scraping. Instead, I recommend manually downloading search results from the database. Below, I provide a step-by-step guide. This method still works as of June 13, 2020. However, please note that ProQuest's download policy could change at any time.

### How to download HTML search results from Ethnic NewsWatch

1. Visit the website of a library that you are a member of (assuming that the library has access to the Ethnic NewsWatch database).

2. Access the database through the library website.

3. Search the database using a specified query. In general, I recommend limiting the search results to `full text`, excluding `duplicates` (one of the `page options`), and increasing items per page to `100` for high data quality and efficient data processing.

4. Select all the items that have appeared on a specific webpage. (If your search yields 1,000 items, you should have 10 pages.)

5. Click the `...` action button and save the search result using the `PRINT` option. There are multiple file formats for saving the search result, but `HTML` is the most flexible for extracting various attributes. The printed search result should look like the sample screenshot in Figure 1.

<img src="https://github.com/jaeyk/ITS-Text-Classification/blob/master/misc/screenshot.png" width="800">

Figure 1. Sample screenshot.

6. Save the printed search result (an HTML file) in a directory.

7. Manually repeat steps 4–6 of the process. Caution should be taken, as automating this process will violate ProQuest's Terms of Use.

## Usage

Now, you have a list of unstructured HTML files. Collecting the data has been tedious. By contrast, turning these files into a tidyverse-ready dataframe is incredibly easy and lightning fast with the help of `tidyethnicnews`. The parsed text data has four columns: `text`, `author`, (publication) `date`, and `source` (publisher). Its rows are newspaper articles. You can enrich the text data by adding other covariates, searching key words, exploring topics, and classifying main issues.

### 1. `html_to_dateframe()`: Turn an HTML file into a dataframe

As a test, select one of the HTML files and inspect whether the result is desirable.

```r

# Load library
library(tidyethnicnews)

# You need to choose an HTML search result
filepath <- file.choose()

# Assign the parsed result to the `df` object
df <- html_to_dataframe(filepath)
```

The `df` object should have four columns: `text`, `author`, `source`, `date`. According to the performance test done by the `microbenchmark` package, the `html_to_dataframe()` function takes average **0.0005** seconds to turn **100** newspaper articles into a tidy dataframe.

This function helps you clean and wrangle your text data without needing to know anything about parsing HTML and manipulating string patterns (regular expressions).

**Notes on the Error Messages**

If an article is missing an `author` field, a value in the `source` field is moved to the `author` field and `NA` is recorded in the `source` field during the wrangling process. When this issue occurs, you will receive an error message saying:

> "NAs were found in source column. The problem will be fixed automatically."`

As stated in the message, the problem will be fixed automatically and there's nothing you need to worry about. If you are not assured, then please take a look at the source code.

### 2. `html_to_dataframe_all()`: Turn all HTML files, saved in a directory, into a dataframe

The next step is scale-up. The `html_to_dataframe_all()` function allows you to turn all the HTML files saved in a particular directory into a d dataframe using a single command.

Again, the `df_all` object should have four columns: `text`, `author`, `source`, `date`. I tested the running time performance using the `tictoc` package. If you use parallel processing, the `html_to_dataframe_all()` function takes **12.34** seconds to turn **5,684** articles into a dataframe. (On average, **0.002** seconds per article.)

```r

# Load library
library(tidyethnicnews)
library(furrr) # for parallel processing 

# You need to designate a directory path.
dirpath <- tcltk::tk_choose.dir()

# Parallel processing 
n_cores <- availableCores() - 1

plan(multiprocess, # multicore, if supported, otherwise multisession
     workers = n_cores) # the maximum number of workers

# Assign the parsed result to the `df_all` object
df_all <- html_to_dataframe_all(dirpath)

```

## How to cite

If you would like to cite, please do something like the following:

```
Jae Yeon Kim. (2020). tidyethnicnewws. R package version 0.1.0. Retrieved from https://github.com/jaeyk/tidyethnicnews
```
