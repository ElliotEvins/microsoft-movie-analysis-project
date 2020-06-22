# Introduction
## Executive Summary
This report provides an analysis and evaluation of the current and prospective profitability of the original content development industry. Methods include investment and budget analysis, quantitative regression, and qualitative assessment. Other calculations include return on investment and genre popularity analysis. All calculations can be found in the associated notebooks. Results of data analyzed produce suggested schedules in projecting revenue and roi per a given production budget as well as a film genre distribution. 

The report finds that prospects of Microsoft in the film industry will require significant investment and a strategic approach to the development of original content. The major areas of opportunity require further investigation but this analysis provides a template in projecting revenue and narrows the choice of genre development. 

Recommendations discussed include:
- Investing in production budget and maximizing revenue generation in order to garner popularity and a profitable return on investment. 
- Distributing content production among the identified genre ratios depending on whether popularity or revenue generation is more valuable to the firm.
- How to percieve nominal vs. real figures in looking at previous productions in making decisions for the fututre.
- Seasonality and individual director/writer considerations.

The report also investigates the fact that the analysis conducted has limitations. Some of the limitations include:
- Monetary figures are not nominalized, giving extra weight to more recent films. 
- Projections derived from regression analysis bare statistical shortcomings that inhibit suggestions from providing use outside their given ranges.
- Further analysis could utilize additional resources in order to identify additional content development strategies.

## Table of Contents
* [ETL - The Rise of Big Chungus](#etl---the-rise-of-big-chungus)
* [Technologies](#technologies)
* [Follow the Money](#follow-the-money)
* [Popularity, Revenue, and Genre Guidance](#popularity-revenue-and-genre-provide-guidance)
* [Inflation Turns Out to be Thin Air](#inflation-turns-out-to-be-thin-air)
* [Future Investigation](#future-investigation)
* [Recommendations](#recommendations)
* [Conclusions](#conclusion)

#### Terms
- **FoI:** feature of interest
- **pop:** popularity
- **dG:** domestic gross revenue
- **wG:** worldwide gross revenue
- **pB:** production budget

## Technologies 
This project was created using the following languages and libraries. An environment with the correct versions of the following libraries will allow re-production and improvement on this project.
Python version: 3.6.9
Matplotlib version: 3.0.3
Seaborn version: 0.9.0
Pandas version: 0.24.2
Numpy version: 1.16.2

# ETL - The Rise of Big Chungus
This project begins with 9 datasets from 4 sources. After importing each dataset and cleaning the file names we found the following data structure:

![title](/misc/df-1.png)

With so much overlapping information, our approach pivoted towards aggregating these tables into fewer entities that could be stripped down after exploratory analysis. Rather than creating a SQL database we opted to create a large CSV file we could call on for each question - we came to call this master csv “Big Chungus.” We began by merging the IMDB tables that contained a 1 to 1 relationship between the ‘tconst’ key used to identify specific films. Genre information and IMDB’s people key (‘nconst’) for writers and directors were entered as a long string regardless of number of entries. We split these strings into a list that could then be made into individual datum. We expand the table in a wide manner by assigning each genre type to a categorical variable with a value of 1 representing that genre’s association with a given film. Writers and directors are split between 3 new variables for each category with a given film’s primary directors and writers being listed by their tconst value in the order they were provided. Films with fewer than three writers or directors were given Nan values to the latter variables. 

### More Money, More Data

With the IMDB data applicable to this table successfully merged, we turned towards the other tables for monetary information. With more values and FOI in The Numbers’ database, we chose to merge this file with Big Chungus over the bom_movie_gross table. Without a tconst value in this table, we chose to use the film title as a means of looking up the IMDB key and removing duplicates before analysis. This inevitably results in some data loss but since we are providing information to one of the largest companies to have ever existed, we assume it is safe to filter duplicate values by the higher pB. We cleaned the movie titles by removing spaces, punctuation, capital letters and any other odd characters we could identify. From there we were able to apply the same cleaning method to the IMDB title database and merge almost 4000 of the data table’s movies with a corresponding tconst value. We also cleaned the monetary figures and normalized them to integers. We then merged this table with Big Chungus along with a similarly normalized tmdb table (with datetime formatting) to create the following data layout:*

![title](/misc/df-3.png)

*Note that imdb.name.basics.csv and imdb.title.principals.csv were also merged.

# Follow the Money
This business isn’t for the faint of heart - if you want to succeed you first have to define what success looks like. As a company, does microsoft want to make money? Do they want to enter this space to hurt their competition? Or is the tech-giant just looking for some of the prestige that comes from making your mark in the world of original content? We’ve leveraged our recently extracted data from multiple sources to understand how anyone looking to enter the movie business should spend money if they hope to make money. We used this notebook to delve deep in the budgets (pB) and revenue (wG) of previously released films and to formulate reasonable expectations of Microsoft’s return on investment for any given pB. 

## Spend Money to Make Money

From the primary data table (big_chungus.csv), we extract and clean the data we hoped would answer this question. These features of interest were 'tconst','primary_title', 'release_date', 'domestic_gross', 'worldwide_gross','production_budget'. Since domestic_gross, worldwide_gross, and production_budget contained the same amount of values and wielded the most relevance to the question being asked, we selected these three FOI as the basis for removing Nan values from Big Chungus. From here we explored the FOI and sought correlation. Because profitability is a factor in our process, we created new variables that described the net return on investment both domestically and worldwide [(wG - pB)/ pB)]. As one would expect, many of the fiscally related features yielded high correlation as it’s usually safe to assume that when a movie grosses well domestically, it can usually perform well worldwide. From looking at the scatter matrix, the relationship between pB and wG seemed worth diving into:

![title](/misc/scatter-matrix.png)

### What’s a Normal Budget?

On closer examination of the gross figures, wG seems to encapsulate the dG so we chose to move forward with that figure alone. The vast majority of movies in this data were produced with pB less that 2 million dollars so we defined a “normal budget” as one that was at or below 150 million. A closer look also shows that all of the films with a pB over 150 million act as outliers when charted between wG and pB:

![title](/misc/wG&pB.png)

Once normal pB were defined as less than 150 million, we were able to fit a regression line that could act as a model for Microsoft to use when projecting wG based on their pB. The line of best fit through the nearly 4,000 films used in this calculation showed a slope of 2.5, which tells Microsoft that for every dollar they invest up to $150 million in pB, they can expect a return of $2.50 in wG revenue. In pBs over 150 million, the pattern does not apply as effectively but a slope of 3.8 indicates that these movies typically return even more revenue (for an extended discussion of regression technique applied, see [Quantified the Relationship](###quantified-the-relationship). 

![title](/misc/regress-budgets.png)

## If Microsoft Makes a Lot of Money, How Much Money Will It Make?

With a defined relationship between pB and wG, we turn our focus to worldwide return on investment(ROI). With a large majority of films yielding a -1 to 1 return on investment, we chose this as a definition of normal ROI. With this cap on earnings and loss, we once again map out pB and wG but overlay ROI. An obvious pattern emerges that shows films with maximum ROI come from movies that maximize revenue generation compared to their pB. 

![title](/misc/roi-spectrum.png)

The idea that max ROI comes from movies that make the most revenue in relation to their pB may seem obvious but establishing this foundation from the data allows us to quantify this prospect through a regression formula. By narrowing the field to profitable films we are able to instruct Microsoft on how much the company should expect in wG for any given pBin order to achieve a positive ROI. 

![title](/misc/best-roi.png)

This regression gives us a formula to calculate revenue goals for a given pB. As an example.
If Microsoft plans to make a film with a $1,000,000 pB then:

Expected wG = 1.5 * $1,000,000.00 + $142,903.50
Expected wG = $1,642,903.50

Thus for a pB of 1,000,000 USD, Microsoft should anticipate and strive towards wG of $1,642,903.50.

# Popularity, Revenue, and Genre Provide Guidance
[Notebook Associated with this Section](https://github.com/ElliotEvins/microsoft-movie-analysis-project/blob/master/ipynb%20files/q2where_the_money.ipynb)

Having established expectation guidance with respect to pB and wG, we sought to explore other features that may inform budgetary decisions when developing the production portfolio. Two standout features were genre and popularity.
## Popularity and Revenue
### Isolated and cleaned relevant features
The features of interest (FoI’s) were: popularity (pop), domestic gross revenue (dG), worldwide gross revenue (wG), and production budget (pB). We assumed values were in United States Dollars (USD) or, at the very least, comparable denominations since they were retrieved from the same source (U.S based data collection companies). After finding greater correlative strength in wG than in dG to other FoI’s, we chose to focus our analysis on the former. Lastly, we eliminated null observations within our two central FoI’s: pop and wG.
### Quantified the relationship
To improve readability, we normalized pop and wG (see technical journal 2 for details). To identify a quantitative relationship between pop and wG, we used simple linear regression:

regression_line = r (sY/sX) + (x_barY - x_barX), where

r = correlation between x and y,
sN = standard deviation of n
x_barN = sample mean of N
Y = criterion variable
X = predictor variable

Assigning pop as the criterion variable and wG as the predictor variable, we found a significant linear relationship which we represented graphically as follows:

![alt text](/misc/best-roi.png "linregress_bt_pop_and_wG_link")

This connection can be described in terms of the following measures:
r = 0.53
p = 5e-224

Once we established the relationship between popularity and world gross revenue (a proxy for ROI), we wanted to see what makes a film popular and if that matches revenue performance.
## Correlates of Genre to Popularity and Revenue
After running preliminary correlative detection on the aggregated title data, we determined that genre was a useful axis along which to evaluate variation.
### Isolated and cleaned relevant features
We isolated all genres, wG, and pop. We cleaned worldwide gross by eliminating zeroes. Genres did not require cleaning because they were drawn from previously cleaned aggregated data.
### Quantified the relationship
We produced two correlation charts. The first shows wG’s correlation to each genre. The second shows pop’s correlation to each genre. These were then used to generate graphics to help intuit the relative correlative strength, both positive and negative, between features.

![alt text](/misc/gross_corr.png "corr_wG_genre_link")
![alt text](/misc/pop_corr.png "corr_pop_genre_link")
### Quantitative Guidance
We standardized the correlations in both charts through the following equation:

pct = (corr + |min_n|) / rf, where
	pct = percentage
	corr = correlation
	|min_n| = absolute value of lowest n
	rf = ratio factor (used to standardize to 100%)

While none of the correlations were particularly powerful on their own, by standardizing them to a share of a whole, we were able to then produce a percentage schedule. The percentage schedule can be used by stakeholders to inform budgetary decisions and evaluate investment distribution opportunities.

![alt text](/misc/gross_cor_schedule.png "schedule_wG_genre")
![alt text](/misc/pop_corr_sched.png "schedule_pop_genre")

# Inflation Turns Out to be Thin Air
## If We’re Talking About Money, Should we Talk About REAL Money?

[In this notebook](https://github.com/ElliotEvins/microsoft-movie-analysis-project/blob/master/ipynb%20files/q3-elliot-inflation.ipynb) we explore the validity of our previous suggestions by reassessing the methods used through the lens of inflation adjustment. More background on this approach is available in the associated [blog post.](https://medium.com/@elliotwevins)

There is an asterisk associated with any fiscal discussion in the production of movies and that’s an ongoing debate over how figures should be calculated over time. Through the consumer price index, economists have ways of translating exactly how much a dollar was worth during x time period compared to what it is worth at y. Studio obfuscation and plain difficulty in gathering accurate information have also made production budgets and revenue generation difficult figures to confirm. With this in mind, we wanted to check our previous recommendations against real figures rather than the nominal information provided by The Numbers database. 

Once again calling on Big Chungus, we clean the data in the same way as [Follow the Money](#follow-the-money) and extract the year from the release date column. We use Python’s [CPI module](https://github.com/datadesk/cpi) to convert budget and gross figures to inflation adjusted dollars. This module’s latest update is in line with the October, 2018 CPI so the 56 films in the database made after the release of those figures were reverted to that benchmark. Through our previous investigation we immediately see that this consolidates the gross revenue figures with the exception of a few outliers but budget remains largely unchanged along the x axis. Instead of 150 million acting as a natural cutoff in these figures, the condensed nature makes 100 million real dollar budgets a natural floor and ceiling to investigate. 

![title](/misc/inflation-adjusted-eda.png)

Running the same regression analysis for the best fitting line on the much flatter inflation adjusted figures beneath 100 million yields little results with an r-value of 0.23. On the other hand, inflation adjusted movies with budgets over 100 million gained statistical significance and income projection. 

![title](/misc/inflation-regression.png)

With the lack of statistical significance in the majority of films after budgets and revenue were adjusted for inflation, it stands to reason that our original model will ultimately serve Microsoft with more accurate working projections. With these adjusted figures, we can also revisit our discussion of genre to see how the percent schedule is affected when figures are adjusted for inflation.

## Inflation Adjusted Genre

Running an identical investigation into the correlation of revenue figures and genre classification, we see a slight decrease in most of the categories but nothing too shocking. Much of the order remains the same but the scale of positive correlation cuts back significantly as ‘Adventure’ drops from near .40 to .24. To better understand this change we create another percentage schedule based on the real figures, subtract the old percentages and graph the delta here. As the graph shows, we learn more about which genres are less successful from these figures:

![title](/misc/real-genre-correlation.png)

While these figures are small differences from the original percentage schedule, they are included in the following visualizations for the consideration of Microsoft though the original schedule is still recommended. Beyond the lack of statistical strength found in the inflation adjusted figures, it stands to reason that the original intent of this investigation was to look at the content that is going to return revenue/garner popularity in this day and age. In this sense the natural weight that inflation gives to more recently released films can be seen as a means of increased accuracy in the formation of our previous models and recommendations. 

![title](/misc/percentage-schedule-adjustment.png)


# Future Investigation
Other patterns emerged from the data that, while unrelated to our primary results, may prove fruitful if given additional attention in future investigations.
## Seasonality
We looked to see if there was any seasonality to movie release dates. That is, we sought to determine whether there was a relationship between time of year and release date. To do so, we converted release date to a day of the year (e.g. February 1 is 32) and counted the number of releases as grouped by each day. This gave us the following non-linear curve.

![alt text](/misc/seasonality.png "seasonality")

As can be seen, there is a clear pattern with a modestly narrow confidence interval. However, we were unable to identify any connection to ROI. Further investigation is recommended.
## Directors and World Gross
We also looked to see if there were patterns in people who worked on certain films. Specifically, we looked at directors and their relationship to wG. While typical directors are associated with films grossing 24 billion USD, top performing directors may be associated with films grossing as high as 1.1 trillion USD -- a difference of 2 orders of magnitude. 

![alt text](/misc/director.png "directors_v_gross")

However, the term performer may be misleading because it’s possible that the success of films is independent from, or only mildly influenced by, directors or any other member of crew. Further investigation is recommended.
# Recommendations
## Use projected worldwide gross to set benchmarks for different budgets
Using the linear regression equation of pB vs wG developed in this repository, Microsoft should be able to determine expected wG for each dollar pB. The strongest correlation falls within the range of .05 to 1 ROI and is, therefore, the most reliable predictive range. Outside this range, is more uncertain and, at this nascent stage, inadvisable.
## Use the genre schedule to determine budgetary focus
There are two options with respect to genre selection: (1) a limited initial release of a few high performing genres or (2) a more balanced portfolio of genres to weather variability. In either case, using the genre schedule developed in this repository should allow for informed determinations of budget distribution.
## Research or Follow Seasonality and Directors
### Research or Follow Existing Seasonality
There’s presumably a reason that existing film production companies release most films at the same two times of the year (and less so at an interceding time of year). That reason was not a direct correlation to ROI. However, there may be other competitive advantages worth investigating. If further investigation is not pursued, it is recommended that, where possible, Microsoft should follow this pattern to avoid any unknown disadvantage.
### Research or Hire Directors
We did not identify a consistent relationship between directors and wG. However, the comparatively enormous yield of certain directors’ works makes further teasing out of directorial impact a valuable research question. Barring additional investigation, Microsoft should, where possible, use directors with past successes.
# Conclusion
This broadly scoped analysis yielded actionable insights and opportunities for further investigation. The findings herein will help narrow the field of possibility in Microsoft’s decision making process and increase the chances of sustainability and success in this exciting new business venture. While the preceding investigations are based on significant bodies of data, the deliverables and conclusions reached are intended to act as guidelines and a starting point for future investigations. Multiple avenues of further exploration have been presented over the course of this investigation and we have aimed to clearly identify our thinking for each step of the research process. Moving forward our findings, schedules, and formulas - presented here as deliverables - should be revisited with updated data and Microsoft can and should compare realized products with these projections. 
