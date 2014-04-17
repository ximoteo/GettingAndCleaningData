Week 2 Quiz
===========

The due date for this quiz is Sun 20 Apr 2014 4:30 PM PDT.

Load packages.


```r
packages <- c("data.table", "sqldf")
sapply(packages, require, character.only = TRUE, quietly = TRUE)
```

```
## Warning: package 'sqldf' was built under R version 3.0.3 Warning: package
## 'gsubfn' was built under R version 3.0.3
```

```
## Loading required package: proto Loading required namespace: tcltk Loading
## required package: DBI
```

```
## data.table      sqldf 
##       TRUE       TRUE
```


Fix URL reading for knitr. See [Stackoverflow](http://stackoverflow.com/a/20003380).


```r
setInternet2(TRUE)
```



Question 1
----------

> Register an application with the Github API here https://github.com/settings/applications. Access the API to get information on your instructors repositories (hint: this is the url you want "https://api.github.com/users/jtleek/repos"). Use this data to find the time that the datasharing repo was created. What time was it created? This tutorial may be useful (https://github.com/hadley/httr/blob/master/demo/oauth2-github.r). You may also need to run the code in the base R package and not R studio.


```r
library(httr)
```

```
## Warning: package 'httr' was built under R version 3.0.3
```

```r
require(httpuv)
```

```
## Loading required package: httpuv
```

```
## Warning: package 'httpuv' was built under R version 3.0.3
```

```r
require(jsonlite)
```

```
## Loading required package: jsonlite
```

```
## Warning: package 'jsonlite' was built under R version 3.0.3
```

```r

# 1. Find OAuth settings for github: http://developer.github.com/v3/oauth/
oauth_endpoints("github")
```

```
## <oauth_endpoint>
##  authorize: https://github.com/login/oauth/authorize
##  access:    https://github.com/login/oauth/access_token
```

```r

# 2. Register an application at https://github.com/settings/applications
# Insert your values below - if secret is omitted, it will look it up in the
# GITHUB_CONSUMER_SECRET environmental variable.  Use http://localhost:1410
# as the callback url
myapp <- oauth_app("quiz2", "ddb0d599de51ccd02f4b", secret = "6af1109f6ecf442d292425087d49bb13d9bbe9c8")

# 3. Get OAuth credentials
github_token <- oauth2.0_token(oauth_endpoints("github"), myapp)
```

```
## Error: oauth_listener() needs an interactive environment.
```

```r

# 4. Use API
req <- GET("https://api.github.com/users/jtleek/repos", config(token = github_token))
```

```
## Error: object 'github_token' not found
```

```r
stop_for_status(req)
```

```
## Error: object 'req' not found
```

```r
output <- content(req)
```

```
## Error: object 'req' not found
```

```r
list(output[[4]]$name, output[[4]]$created_at)
```

```
## Error: object 'output' not found
```



Question 2
----------

> The sqldf package allows for execution of SQL commands on R data frames. We will use the sqldf package to practice the queries we might send with the dbSendQuery command in RMySQL. Download the American Community Survey data and load it into an R object called
> 
> `acs`
> 
> https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv 
> 
> Which of the following commands will select only the data for the probability weights that are formatted like pwgtp1, pwgtp2, pwgtp3, etc. for the people with ages less than 50?


```r
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06pid.csv"
f <- file.path(getwd(), "ss06pid.csv")
download.file(url, f)
acs <- data.table(read.csv(f))
query1 <- sqldf("select pwgtp1 from acs where AGEP < 50")
```

```
## Loading required package: tcltk
```

```r
query2 <- sqldf("select pwgtp1 from acs")  ## NO
query3 <- sqldf("select * from acs where AGEP < 50 and pwgtp1")  ## NO
query4 <- sqldf("select * from acs where AGEP < 50")  ## NO
identical(query3, query4)
```

```
## [1] TRUE
```


Question 3
----------

> Using the same data frame you created in the previous problem, what is the equivalent function to unique(acs$AGEP)


```r
gold <- unique(acs$AGEP)
query1 <- sqldf("select distinct AGEP from acs")
query2 <- sqldf("select AGEP where unique from acs")
```

```
## Error: RS-DBI driver: (error in statement: near "unique": syntax error)
```

```r
query3 <- sqldf("select unique * from acs")
```

```
## Error: RS-DBI driver: (error in statement: near "unique": syntax error)
```

```r
query4 <- sqldf("select unique AGEP from acs")
```

```
## Error: RS-DBI driver: (error in statement: near "unique": syntax error)
```

```r
identical(gold, query1)
```

```
## [1] FALSE
```

```r
identical(gold, query2)
```

```
## [1] FALSE
```

```r
identical(gold, query3)
```

```
## [1] FALSE
```

```r
identical(gold, query4)
```

```
## [1] FALSE
```



Question 4
----------

> How many characters are in the 10th, 20th, 30th and 100th lines of HTML from this page: 
> 
> http://biostat.jhsph.edu/~jleek/contact.html 
> 
> (Hint: the nchar() function in R may be helpful)


Question 5
----------

> Read this data set into R and report the sum of the numbers in the fourth column. 
> 
> https://d396qusza40orc.cloudfront.net/getdata%2Fwksst8110.for 
> 
> Original source of the data: http://www.cpc.ncep.noaa.gov/data/indices/wksst8110.for 
> 
> (Hint this is a fixed width file format)