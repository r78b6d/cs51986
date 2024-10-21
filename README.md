java c
Stat 535 - Midterm Project
Due: Wednesday, 10/16/2024, 6:00pm, EST
Part I - ScrapingIn the ﬁrst part of this exam, we will scrape data about the “Top 100 Greatest Movies of All Time” according  to one IMDb content manager.  You can ﬁnd the list here:  https://www.imdb.com/list/ls055592025/ Speciﬁcally, we will ﬁnd and organize in a data frame. information that appears in the top part of the summary for each ﬁlm:

Figure 1: What we will scrape
That is, year of release, duration, content rating, average rating, description and more (but not the “Metas- core”).  Turns out that this information is stored in a JSON object. We will convert the JSON object using a built-in function, and will also write code from scratch that will get us some of the information from this object. If we work correctly, both data frames - created from the built-in function and from our code - should be identical.


We will use the following packages during this assignment:


library (tidyverse)
library (jsonlite)


In order to ensure we all have the same version of the web page, please load a version that was archived and is shared via Canvas (there should be a warning message when you read the ﬁle, ignore it):
imdb  <- readLines ("Top  100  Greatest Movies  of All Time  (The  Ultimate  List) .html")                 
## Warning  in  readLines("Top  100  Greatest  Movies  of All Time  (The  Ultimate
## List) .html"):  incomplete  final  line  found  on  ' Top  100  Greatest  Movies  of  All ## Time  (The  Ultimate  List) .html '
1.  [3 pt] What is the type of data of the variable imdb?  What are its dimensions?
1.  [5 pt] Identify the indices in  imdb in which the expression “Godfather” appears.  How many characters are in each of the strings that include the expression “Godfather”?
2.  [5 pt] Create a new variable called db that is character vector of length one, its only element the string that contains the expression “Godfather” whose number of characters is 62,533.
3.  [10 pt] The JSON object we are interested in appears inside  script. HTML tags.  Speciﬁcally, the JSON object appears exactly between the expression  and
.
•  Show that the string  appears only once in db.
•  Show that the string  appears only once in db.
•  Extract  into  a  new  variable  called  db_json  only  the  characters  that  appear  between   and .
The Easy WayAs mentioned above, we can use a built-in R function to read the JSON object directly into a list in R. This  is the purpose of the function fromJSON from the jsonlite package.  It will read the JSON object into a hierarchical list.  One of the elements of this list is the data frame. we are interested in.  Please execute the  following code to extract it to the variable db_df (the code somewhat simpliﬁes the resulting data frame, omits the irrelevant type variable, and omits the alternateName variable which can be dealt with for extra credit - see below):


db_list  <- fromJSON (db_json)
db_df  <-
db_list$itemListElement$item %>%
select (-、@type、)  %>%
unnest_wider(aggregateRating) %>%
select (-、@type、,  -alternateName)


db_df should be a data frame. with 100 observations (one per movie) and 11 variables.
The Hard Way
Our task is now to extract the same 11 ﬁelds from the raw JSON object and create a data frame. that is identical to the db data frame.  These ﬁelds are:


db_fields  <-
c(






"url" ,   "name" ,
"description" ,
"image" ,
"bestRating" ,
"worstRating" ,
"ratingValue" ,
"ratingCount" ,
"contentRating" ,
"genre" ,
"duration"
)

[45 pt] Write a function called get_json_field that takes two inputs: the ﬁrst is a json_代 写Stat 535 - Midterm ProjectJava
代做程序编程语言object to which the user will pass a character vector of length 1 whose sole element is a string containing a JSON object. The second is field_name to which the user will pass the name of the ﬁeld to be extracted from the JSON object in the ﬁrst input.  The function will return a vector that contains the elements of the corresponding ﬁeld in the appropriate data type.  That is, you need to make sure that your function automatically returns character vectors when the ﬁeld is a character ﬁeld, and integer vector when the ﬁeld in an integer ﬁeld, and a numeric double vector when the ﬁeld contains non-integer numeric values.  Your function should be as general as possible: do not write inside the function separate code for each variable. I.e. you should not treat url, name, bestRating etc explicitly in the code. You should be able to identify the correct type only from the raw JSON input and extract the ﬁeld according to its type.Do not use R functions from packages designed for reading JSON objects, the point here is to use the material we learned in class - treating the JSON object as raw text and extracting information from it using regular expressions.
Once you have the function, write a loop that iterates over db_fields.  For each ﬁeld call the get_json_field function and add the output to a new data frame. db_df_from_json.
Finally, show that the two data frames, db_df and db_df_from_json are identical using the identical() function.
Tip: when you develop your function and regular expressions, you can conveniently use str_view on a part of the JSON object.  Say only the ﬁrst 1000 characters using str_trunc(db_json,  1000).[5 pt] Extra credit:  expand your function  to handle the ﬁeld alternateName as well and add this variable  to the loop that creates  db_df_from_json.  If you  do  so, remove the  expression  -alternateName from the  code that creates db_df, and show that the expanded db_df and the expanded db_df_from_json are identical. [End Extra  Credit]
Part II - Data Manipulations
In Part II we will generate some additional quantitative variables, based the original variables and compute a few descriptive statistics.
[8 pt] 1.  Add to your data frame. a add a column named duration_mins whose values correspond to the duration of each ﬁlm in minutes. What is the average duration of a ﬁlm in this list?[8 pt] 2.  Several ﬁlms have more than one genre. Add to your data frame. a variable that counts the number of genres per ﬁlm.  Provide a table that counts how many ﬁlms are there for every number of genres.  I.e., the table should count how many ﬁlms have 1 genre only, how many ﬁlms have 2 genres, etc.
[8 pt] 3.  Create a new variable that includes only the ﬁrst genre of each ﬁlm.  For each ﬁrst genre, count how many ﬁlms there are in the group, and compute the average duration, rating and rating count for each group of ﬁlms. What is the ﬁrst genre with fewest ﬁlms? Which ﬁrst genre has the highest average rating? Which ﬁrst genre has the longest mean duration?  And which ﬁrst genre has the most raters on average?[8 pt] 4.  Create a new variable that measures the average length of a word in the description ﬁeld (that is, for each description measure ﬁrst the number of characters in each word, and average over those).  For each content rating category, show the ﬁlm that has the longest words on average.
A pool of hints (that may or may not be useful for you, there are many ways to perform. the above tasks correctly):
•  You cannot use "" as a regular expression.  To identify elements of a character vector that are equal to the empty string  "" you may use the regular expression ˆ$.
• https://dplyr.tidyverse.org/articles/programming.html?q=:=#name-injection
•  The function which()
•  The function slice() from the dplyr package.
•  The regular expression  (.*?)





         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
