---
title: "Semester Models (stata) — Data Science of Congress, Power, and Politics"
author: "Sungjoo Yoon — Prof. Jim Snyder"
date: "2024-05-01"
output: html_document
---



## Modeling set 6:
#### 1a)
Dropping candidates with less than 10% of votes:
drop if voteshare < 10;

#### 1b)
New variable for # of candidates in a race:
bysort state district: gen n_cand = _N;

#### 1c)
Dropping uncontested races using prior variable:
drop if n_cand == 1;

#### 1d)
New variable to dichotomize incumbency status:
gen incumbent = (status == "I");

#### 2a)
Using bysort incumbent: summarize received spent, detail;, 

```r
knitr::include_graphics("images/6-2a.png")
```

<div class="figure">
<img src="images/6-2a.png" alt="plot of chunk unnamed-chunk-2" width="2061" />
<p class="caption">plot of chunk unnamed-chunk-2</p>
</div>
Here we get our descriptive statistics for our variables of interest, split by incumbency status. We see that for the ‘incumbent’ group, we have 330 observations. Within this group, the minimum and maximum money received is $0 and $28.7 million. The median received is $1,830,329, and the mean received is $2,960,845 — all while the standard deviation is $3,696,125. Still within this group, the minimum and maximum money spent is $18,500.41 and $25.1 million respectively. The median spent is $1,586,853, and the mean spent is $2,597,748. The standard deviation in this context is $3,106,460. 

For the ‘non-incumbent’ group, we have 415 observations. Within this group, the minimum and maximum money received is $0 and $12.2 million. The median received is $356,059.70, the mean received is $1,386,616, and the standard deviation for said figure is $1,987,764. With respect to spending, the minimum and maximum money spent is $0 and $12.1 million respectively. The median spent is $332,215, the mean spent is $1,350,132, and the standard deviation here is $1,952,688. 

Based on these initial findings, I hypothesize that incumbency status has a positive effect on both money received and spent. With respect to money received, the incumbent median is ~5 times greater and the mean is ~2 times greater than non-incumbents. With respect to money spent, the incumbent median is also approx ~5 times greater, and the mean is ~2 times greater as well. Clearly, there is a trend of incumbents being able to garner and employ a war chest at larger scales. Yet, one limitation is the large standard deviations that we see in our initial statistics.

#### 2b)

```r
knitr::include_graphics("images/6-2b.png")
```

<div class="figure">
<img src="images/6-2b.png" alt="plot of chunk unnamed-chunk-3" width="2155" />
<p class="caption">plot of chunk unnamed-chunk-3</p>
</div>
Based on these plots, it seems that candidates generally spend and receive more money when their vote share is around 50% (i.e. when they aren’t winning by gigantic margins). We can see this by observing the clustered peak of multi-million dollar campaigns around the 50% vote share in both graphs. Yet, when considering which candidates spend/receive the most money (i.e. the extremes / outliers), we see this demographic coming from incumbents with high/comfortable vote shares (seen in the top-right corners of both graphs). Said candidates appear to be the majority of seven-figure campaigns. A hypothesis for why these effects may be seen is because A) there is more urgency for donors to donate in close elections, and B) incumbents with high vote shares have long-term visibility.

#### 2c)
New variable for margin of victory in any given election:
bysort state district (voteshare): gen margin = voteshare[_N] - voteshare[_N-1];

#### 2d)

```r
knitr::include_graphics("images/6-2d.png")
```

<div class="figure">
<img src="images/6-2d.png" alt="plot of chunk unnamed-chunk-4" width="2160" />
<p class="caption">plot of chunk unnamed-chunk-4</p>
</div>
Based on these graphs, there appears to be a slight association between lower margins of victory (i.e. higher competitiveness) and higher amounts of money spent/received. Looking at the cluster in the bottom left of both graphs, we see more data points at both higher levels of receipts/expenditures. Yet, this is not absolute. We can also observe that the extremes are somewhat spread out over the x-axis, and they all tend to be incumbents. Thus, it is difficult to pin competitiveness of district as the primary explanatory factor in variance of finances, but we can make a preliminary assumption that it is one of the significant predictors.

#### 2e)
Using the following code:
regress received incumbent margin;

```r
knitr::include_graphics("images/6-2e.png")
```

<div class="figure">
<img src="images/6-2e.png" alt="plot of chunk unnamed-chunk-5" width="1755" />
<p class="caption">plot of chunk unnamed-chunk-5</p>
</div>
Here, we observe a couple of things. We first see an intercept of approx. $2,828,907 — meaning the expected amount of money fundraised for a given candidate when we control for both incumbency status and margin of victory (i.e. a non-incumbent with a margin of victory of 0). Intuitively, we see a statistically significant p-value of approx. 0.000, although this is not particularly substantive (given that running for office means you definitionally raise money in most cases). 

We also see a coefficient on the incumbency variable of $1,584,081 — this reflects the expected increase in amount of money fundraised (compared to the baseline and controlling for margin of victory = 0) when a given candidate is an incumbent. We see a p-value of approx. 0.000 (reflecting the likelihood of seeing a result this extreme if the null hypothesis were true, that there was no significant difference related to incumbency). Thus, this result is statistically significant and we reject the null — substantively, being an incumbent does, in fact, predict higher fundraising.

On the explanatory variable of margin of victory, we see a coefficient of -$57,762.92. Interpretively, this means that we can expect a $57,762.92 decline in money fundraised per point increase in margin of victory (compared to the baseline and controlling for incumbency status). In essence, less competitive races are associated with less fundraising. Our p-value here is again approx. 0.000 (reflecting the likelihood of seeing a result this extreme if the null hypothesis were true, that there was no significant difference related to margin of victory). Thus, this result is statistically significant and we reject the null — substantively, higher margins of victory does, in fact, predict lower fundraising.

Yet, on the overarch, these results have a severe substantive limitation. Namely, our model only explains for approx. 16.74% of variance (as observed in our adjusted R^2 value). Thus, this regression as a whole does not capture the entire picture (or even a majority of the variance). This serves as a basis for further investigation into what other factors influence campaign income.

#### 2f)
Using my results from the previous part, my hypotheses from parts (a) and (d) are confirmed to a significant yet limited degree. This is because we do see statistically significant increases in fundraising when a candidate is an incumbent, and a statistically significant decrease when a candidate wins by more (essentially mirroring my conjecture). At the same time, the low R^2 reflects that there are many other predictive factors at play, which limits the substantive effect of these conclusions and mirrors the caveats (re: the trend-bucking outliers) outlined earlier as well.

#### 3a)
My Congressman is Rep. Adam Schiff — a Democrat representing the 30th District of California. Notably, this data is for his most recent campaign (for the United States Senate in 2024). The top five industries that Congressman Schiff receives money from are the legal, educational, financial, and independent (retired / progressive advocacy) industries. These all generally fit within the framework of California’s most important sectors (both ideologically and economically, e.g. Hollywood and West Coast law). While approx. 42% of his funding came from individual/small donors, the majority (at approx. 53%) came from large individual contributions. Just 1.8% came from PAC funding. Even when examining his last House election in 2022, this pattern holds (with the same industries/sectors and similar percent breakdowns for the types of donations). Demographically, he draws from both genders rather equally, but larger donations are disproportionately from male donors.

We can take a more granular look at this data as well. When we examine the top 20 contributors, we see some degree of diversity. Namely, there are seven law firms, six investment/property management firms, three California universities, two entertainment companies, an insurance company, and the State of California (which I need to do more research on, because I am confused as to why that is the case). These are not generally surprising, however. Our district is situated near both the hub of West Coast law and property management (i.e. cities like Studio City and donating firms like O’Melveny), contains a wide variety of entertainment companies (like Walt Disney, which is on his list of top 20 donors), and feeds into various elite California universities (~30 minutes away from both USC and UCLA). Thus, many of these contributors seem geographically rooted in his constituency. However, only a little under half of total fundraising is in-state fundraising (at approx. 47%). On the spending end, virtually every major expenditure is on campaign consulting (e.g. Authentic Campaigns), digital marketing (e.g. Amplify.ai), and legal services (e.g. Elias Law Group). 

## Modeling set 5:
#### 1a)
Two of the votes identified by the Heritage Foundation that can be examined are numbers 867 and 74. Roll Call 867 was on the Inflation Reduction Act. It invested in clean domestic energy, put restrictions on increasing prescription drug prices, and reduced the budget deficit by making a large/pre-emptive down payment. It is also considered landmark legislation because it is the largest investment in the climate in American history. This bill was signed into law on August 16th, 2022. Roll Call 74 was on the Bipartisan Background Checks Act, which aimed to place comprehensive background checks on private firearm transfers. Specifically, it sought to address the issue of unregulated sales when the seller is not a registered dealer. This bill passed the House on March 11, 2021, but has since been in limbo (as it has not passed the Senate, and has lost attention). 

#### 1b)
The Heritage Foundation is on the ‘no’ side of every key House vote they’ve identified for a couple of reasons. First, they are ideologically conservative, which implies a preference for less and not more change (especially in the context of these pieces of legislation). Second, all of this legislation is from the 117th Congress, when Democrats had control of both chambers (making it intuitive that they would want to block a progressive-dominated agenda). Third, the structure of the House allows the agenda of the majority to be pushed through easily, leading to a more partisan agenda writ large (meaning conservative groups have all the more reason to care about these votes). This occurs because the majority has relatively unrestricted agenda control (run through the Speaker’s expedition and ‘rules’ requests). In that vein, this would likely not be as analogous in the Senate. While I still expect the Heritage Foundation to score Senators on anti-Democrat voting records, Senate votes are less partisan by the nature of its structure. Not only is agenda control less centralized (given the Senate majority leader has to ‘motion to proceed’ / cannot just exercise the aforementioned mechanisms the Speaker uses), but each individual Senator has more leverage to prevent floor votes on undesired legislation (such as the filibuster). This leads to Senate votes being more temperate/bipartisan than the House (as was intended), which gives ideologically-driven groups such as the Heritage Foundation less of a reason to antagonize certain votes.

#### 1c)
The following code produces the heritage variable:

```r
knitr::include_graphics("images/5-1c.png")
```

<div class="figure">
<img src="images/5-1c.png" alt="plot of chunk unnamed-chunk-6" width="1384" />
<p class="caption">plot of chunk unnamed-chunk-6</p>
</div>

#### 1d)
The scatterplot is pasted below:

```r
knitr::include_graphics("images/5-1d.png")
```

<div class="figure">
<img src="images/5-1d.png" alt="plot of chunk unnamed-chunk-7" width="1027" />
<p class="caption">plot of chunk unnamed-chunk-7</p>
</div>
We can produce this by using the following code:

```r
knitr::include_graphics("images/5-1d1.png")
```

<div class="figure">
<img src="images/5-1d1.png" alt="plot of chunk unnamed-chunk-8" width="1379" />
<p class="caption">plot of chunk unnamed-chunk-8</p>
</div>

#### 1e)
We get these correlation coefficients using the following code:
pwcorr heritage nominate_dim1;
pwcorr heritage nominate_dim1 if party_code == 100;
pwcorr heritage nominate_dim1 if party_code == 200;

#### 1f)
Based on my previous two answers, the interest group and first-dimension DW-NOMINATE score seem to capture the same ideological dimension; yet, this is not to an absolute extent. Beginning with the scatterplot, we can clearly see two clusters of scores. In the bottom left corner, we have a cluster of Democrats, whose scores are relatively towards the lower end of both spectrums. In the top right, we have a cluster of Republicans, whose scores are towards the higher end of both spectrums. Simply put, this all reflects a visually intuitive and clear pattern: if an MC has a low/liberal DW-NOMINATE score, they generally have a low rating by the Heritage Foundation as well. Analysis of the correlation tables confirms a similar trend — when analyzing the correlation between our two factors of interest across all MCs, we get a coefficient of approx. 0.9669 (which is very high). Yet, these tables do provide us some information on the limitations of our data. When filtering down these correlations within parties, we get a coefficient of 0.2670 for Democrats and 0.5812 for Republicans (weak and moderate associations respectively, and significantly lower than when examining all of the data). Interpretatively, this goes to show that when we examine how much of the ideological dimensions are captured within our analysis, the correlation is much weaker when isolating by party affiliation. In other words, these two factors have low predictive power in distinguishing members of the same party. One possible explanation for this is that because the input data for the Heritage rating is more limited than NOMINATE, it has difficulty capturing smaller-yet-prevalent differences between same-party legislators.

#### 2a)
Two of the votes identified by the Associated General Contractors of America that can be examined are numbers 368 and 69. Roll Call 368 was the Infrastructure Investment and Jobs Act, which was enacted into law on November 15, 2021. The ‘Infrastructure’ part of the act invests hundreds of billions of dollars in motor, rail, safety, and other transit-related initiatives. The law also funds broadband access, water access, electric grid renewal, and more. Roll Call 69 was the Protecting the Right to Organize Act, which passed the House but has not had any movement ever since (has stalled in committee for 4 years / thus is not law). This act aimed to amend labor laws — specifically, by strengthening the right to unionize, weaken state-level ‘right-to-work’ policies, allowing for unions to encourage industry-adjacent strikes, and permitting the NLRB in fining companies directly.

#### 2b)
The following code produces the AGC variable:

```r
knitr::include_graphics("images/5-2b.png")
```

<div class="figure">
<img src="images/5-2b.png" alt="plot of chunk unnamed-chunk-9" width="1459" />
<p class="caption">plot of chunk unnamed-chunk-9</p>
</div>

#### 2c)
The scatterplot is pasted below:

```r
knitr::include_graphics("images/5-2c.png")
```

<div class="figure">
<img src="images/5-2c.png" alt="plot of chunk unnamed-chunk-10" width="1453" />
<p class="caption">plot of chunk unnamed-chunk-10</p>
</div>

#### 2d)
We get these correlation coefficients using the following code:
pwcorr heritage nominate_dim1;
pwcorr heritage nominate_dim1 if party_code == 100;
pwcorr heritage nominate_dim1 if party_code == 200;

#### 2e)
Based on my previous two answers, the AGC and first-dimension DW-NOMINATE score seem to capture a similar ideological dimension to some degree; yet, this is to a significantly more limited extent. Beginning with the scatterplot, we can see two clusters of scores. Yet, unlike the Heritage scores, these clusters are significantly less differentiable in terms of their vertical placement. The Democratic cluster is somewhat lower on the y-axis than the Republican cluster, and their primary data nests appear to reflect a similar trend; however, it is not visibly drastic. Simply put, this reflects an ambiguous pattern: if an MC has a low/liberal DW-NOMINATE score, they may have a slightly lower AGC score. Analysis of the correlation tables again confirms a similar trend — when analyzing the correlation between our two factors of interest across all MCs, we get a coefficient of approx. 0.8604 (somewhat high). At the same time, the aforementioned limitations come into play. When filtering down these correlations by party, we get a coefficient of 0.1200 for Democrats and 0.0040 for Republicans (both quite weak associations). Interpretatively, this goes to show that when we examine how much of the ideological dimensions are captured within our analysis, the correlation is again much weaker when isolating by party affiliation.

#### 2f)
Based on my results in questions 1 and 2, I would expect the support/donation patterns of both interest groups to differ based on party. Namely, I would expect the Heritage Foundation to support to/donate to Republicans at a greater rate (given their clear-cut conservative preferences), whereas I would expect the AGC to be more bipartisan (given their more middle-ground preferences).

## Modeling set 4:
#### 1a) 
use "/Users/sungjooyoon/Desktop/gov1300/pset4/ex4_legislation.dta";
merge 1:1 congress using ex4_congress.dta;
drop if n_enacted == .;

#### 1b)
gen pct_enacted = n_enacted / total;
summarize pct_enacted;
This gives us a mean pct_enacted value of .0395809, which reflects the average share of actually enacted bills relative to total legislation per Congress, over the period of our data set.

#### 1c)

```r
knitr::include_graphics("images/4-1c.png")
```

<div class="figure">
<img src="images/4-1c.png" alt="plot of chunk unnamed-chunk-11" width="1269" />
<p class="caption">plot of chunk unnamed-chunk-11</p>
</div>
Here, after using summarize n_enacted;, we see that there are 25 observations for this variable. The reported mean is 515.92 pieces of legislation enacted, with a standard deviation of 177.322. We also see that the lowest number of legislation passed in a Congress was 199, and the highest was 804.

#### 2a)
gen divided_congress = (hcontrol != scontrol);
gen divided_gov = (hcontrol != scontrol) | (hcontrol != pcontrol) | (scontrol != pcontrol);
Generating the two dummy variables using conditionals ^.

#### 2b)
Using: summarize n_enacted if divided_congress == 1;
summarize n_enacted if divided_congress == 0;
summarize n_enacted if divided_gov == 1;
summarize n_enacted if divided_gov == 0;
We get:

```r
knitr::include_graphics("images/4-2b.png")
```

<div class="figure">
<img src="images/4-2b.png" alt="plot of chunk unnamed-chunk-12" width="1512" />
<p class="caption">plot of chunk unnamed-chunk-12</p>
</div>

(i) In other words, our dataset has 6 observations when Congress is divided. In those 6, the average pieces of legislation passed were 469.5, with a standard deviation of 186.6363. The minimum passed under this condition was 284, and the maximum was 687. 

(ii) On the contrary, we have 19 observations of Congress not being divided. Within those, the average pieces of legislation passed were 530.5789, with a standard deviation of 176.927. The minimum passed under this condition was 199, and the maximum was 804.

(iii) We can also examine the figures for when the government as a whole is divided (i.e. either the executive or either half of the legislature is controlled by a different party than the other two). Our dataset has 16 observations for when the government as a whole is divided. In those 16, the average pieces of legislation passed were 530.5, with a standard deviation of 178.1715. Under this condition, the minimum passed was 284, and the maximum was 772. 

(iv) On the other hand, we have 9 observations for when the government is not divided. Within those, the average pieces of legislation passed were 490, with a standard deviation of 183.3746. The minimum is 199, and the maximum is 804. 

#### 2c)
Using ttest n_enacted, by(divided_congress); (+ the same model with assumptions of unequal; variance), we get:

```r
knitr::include_graphics("images/4-2c.png")
```

<div class="figure">
<img src="images/4-2c.png" alt="plot of chunk unnamed-chunk-13" width="1560" />
<p class="caption">plot of chunk unnamed-chunk-13</p>
</div>
Under the first t-test (assuming equal variances), we get a t-statistic of 0.7203. We can also clearly see that the two-tailed t-test returns a p-value of approx. 0.4738. Using an alpha of 0.05, this p-value is higher than said value. Therefore, the difference in means is not statistically significant in this case. We fail to reject the null hypothesis that there is no difference in means, and it is likely the difference (between divided and undivided Congresses in relation to number of bills enacted) can be ascribed to chance. This remains the same under an assumption of unequal variances, where the t-statistic is 0.7075, and the p-value is 0.4992.

#### 2d)
Using ttest n_enacted, by(divided_gov); (+ the same model with assumptions of unequal; variance), we get:

```r
knitr::include_graphics("images/4-2d.png")
```

<div class="figure">
<img src="images/4-2d.png" alt="plot of chunk unnamed-chunk-14" width="1317" />
<p class="caption">plot of chunk unnamed-chunk-14</p>
</div>
Under the first t-test (assuming equal variances), we get a t-statistic of -0.54. We can also clearly see that the two-tailed t-test returns a p-value of approx. 0.5944. Using an alpha of 0.05, this p-value is higher than said value. Therefore, the difference in means is not statistically significant in this case. We fail to reject the null hypothesis that there is no difference in means, and it is likely the difference (between divided and undivided governments in relation to number of bills enacted) can be ascribed to chance. This remains the same under an assumption of unequal variances, where the t-statistic is -0.5355, and the p-value is 0.5995.

#### 2e)
(i) There does not appear to be a statistically significant relationship between a divided Congress and the number of enacted bills passed. After running our t-tests, we find that the difference in means of bills passed in divided and undivided Congresses does not meet our significance level (and thus can be ascribed to chance)—this suggests that divides in legislative control have no bearing on productivity.

(ii) There also does not appear to be a statistically significant relationship between a divided Congress and the number of enacted bills passed. After running our t-tests, we find that the difference in means of bills passed in divided and undivided governments does not meet our significance level (and thus can be ascribed to chance)—this suggests that divides in government control have little-to-no bearing on productivity.

#### 3a)
gen gap_median = abs(median_D - median_R);
Here, we generate a dummy variable that is the absolute value of the difference between the two parties’ median DW-NOMINATE scores.

#### 3b)
Using summarize gap_median;, we get:

```r
knitr::include_graphics("images/4-3b.png")
```

<div class="figure">
<img src="images/4-3b.png" alt="plot of chunk unnamed-chunk-15" width="1635" />
<p class="caption">plot of chunk unnamed-chunk-15</p>
</div>
Interpretively, the mean of said variable represents the average gap in the median ideological indexes between the two parties (on the first dimension DW-NOMINATE scale). Clearly and in that context, we have 25 observations for the data, with an average median ideological gap of 0.7284 and a standard deviation of 0.098333. The minimum gap for any given Congress was 0.589, and the maximum was 0.875.

#### 3c)
Using scatter n_enacted gap_median, xtitle("Median Party Ideological Gap (1st Dim. DW-NOM)") ytitle("# of Bills Enacted in a Session") title("Number of Bills Enacted w.r.t. Median Ideological Gap (DW-NOM)");, we get:

```r
knitr::include_graphics("images/4-3c.png")
```

<div class="figure">
<img src="images/4-3c.png" alt="plot of chunk unnamed-chunk-16" width="1488" />
<p class="caption">plot of chunk unnamed-chunk-16</p>
</div>

#### 3d)
Here is the code for our regression and adding the line of best fit to the scatterplot:
regress n_enacted gap_median;
scatter n_enacted gap_median, xtitle("Median Party Ideological Gap (1st Dim. DW-NOM)")
ytitle("# of Bills Enacted in a Session")
title("Number of Bills Enacted w.r.t. Median Ideological Gap (DW-NOM)") || lfit n_enacted gap_median;
This gives us the following scatterplot:

```r
knitr::include_graphics("images/4-3d.png")
```

<div class="figure">
<img src="images/4-3d.png" alt="plot of chunk unnamed-chunk-17" width="1827" />
<p class="caption">plot of chunk unnamed-chunk-17</p>
</div>
With regards to the values from the regression, we see an estimated intercept of 1,681.803 and a coefficient for the variable gap_median of -1,600.608.

#### 3e)
(i) You can infer a relationship between the number of enacted bills passed and the difference between the two parties’ ideological positions. We know this because our statistical analysis provided us a pretty substantial negative coefficient (as seen in the previous question), and provided us with a p-value of approx. 0.000 for it (far below virtually any significance level, meaning that there is almost no chance that this coefficient could be ascribed to chance). 

(ii) Continuing on the line of reasoning from above, the negative coefficient suggests that as the ideological gap between parties increases, the number of bills enacted decreases significantly. A likely explanation for this is that larger ideological gaps lead to greater disagreement within parties. This, in turn, leads to greater obstructionism on the part of the minority party (given the majority’s agenda is likely to be even further from their desires) and less common ground for bipartisan policy-making (thus causing less legislation to be passed). In simpler terms, polarization may lead to gridlock.

#### 4a)
gen maj_hmargin = abs(dsh - rsh);
gen maj_smargin = abs(dss - rss);
Here, we generate two dummy variables. The first is the margin the majority has in the House, and the second is the margin the majority has in the Senate (measured in number of seats).

#### 4b)
Using summarize (maj_hmargin); and summarize (maj_smargin);, we find that:
For the first variable (the House margin), there are 25 observations (representing 25 Congresses), with an average margin of 60.04 seats and a standard deviation of 41.01979. The maximum margin was 149 seats, and the minimum was 9. For the second variable (the Senate margin), there are also 25 observations, with an average margin of 9.52 seats and a standard deviation of 6.615134. The maximum margin was 24 seats, and the minimum was 0. The table is pasted below.

```r
knitr::include_graphics("images/4-4b.png")
```

<div class="figure">
<img src="images/4-4b.png" alt="plot of chunk unnamed-chunk-18" width="1677" />
<p class="caption">plot of chunk unnamed-chunk-18</p>
</div>
Interpretively, the means in the summary statistics simply reflect the marginal number of seats the majority party has in either portion of the legislature.

#### 4c)
Using regress n_enacted maj_hmargin maj_smargin;, we get:

```r
knitr::include_graphics("images/4-4c.png")
```

<div class="figure">
<img src="images/4-4c.png" alt="plot of chunk unnamed-chunk-19" width="1672" />
<p class="caption">plot of chunk unnamed-chunk-19</p>
</div>
Here, we see an estimated intercept of approx. 334.4472, a coefficient for maj_hmargin of  approx. 2.656575, and a coefficient for maj_smargin of approx. 2.307985. Interpretively, the intercept represents the value of bills enacted when the majority margins in both legislatures are 0. Further, the coefficients represent the increase in bills enacted associated with a one-seat increase in the maj. margin (in the House/Senate respectively). Yet, given the p-values, only the House margin and intercept variables are statistically significant at a = 0.05 (meaning the Senate margin could be ascribed to chance).

#### 4d)
Using regress n_enacted maj_hmargin maj_smargin vetoed;, we get:

```r
knitr::include_graphics("images/4-4d.png")
```

<div class="figure">
<img src="images/4-4d.png" alt="plot of chunk unnamed-chunk-20" width="1675" />
<p class="caption">plot of chunk unnamed-chunk-20</p>
</div>
The variable of choice, vetoed, may have an effect/relationship on legislative productivity in two ways (statistical analysis will reveal which it supports more). First, increased usage of vetoes may decrease legislative productivity given that it ‘cancels’ legislation that has passed both chambers. However, increased usage of vetoes may also reflect increases of legislative productivity given that presidents often increase veto usage when both chambers are opposed to their agenda (as a measure of ‘last defense’, e.g. Truman fighting the anti-New Deal Republican control of Congress by exercising said power frequently).

#### 4e)
Here, we can clearly see an estimated intercept of approx. 311.4197, a coefficient for maj_hmargin of  approx. 1.91395, a coefficient for  maj_smargin of approx. -1.130139, and a coefficient for vetoed of approx 9.538562. Interpretively, the intercept represents the value of bills enacted when the three variables of interest (House/Senate margin and bills vetoed) are at 0. Moreover, the maj_hmargin and maj_smargin coefficients represent the increase/decrease in bills enacted associated with a one-seat increase in the majority margin (in the House/Senate respectively). The vetoed coefficient represents the increase of bills enacted associated with another successful veto. Substantively, however, only the intercept and the coefficient attached to vetoed is significant at a = 0.05 (and maj_hmargin is close to / weakly significant at 0.053). This is given their respective p-values; otherwise, much of it can be ascribed to chance. A final sidebar is that more vetoes reflect higher legislative productivity, lending support to the second theory as outlined in question 4 part d.

#### 4f)
My results tell us that we can expect higher legislative activity associated with a larger majority margin in the House (more clearly defined in the first regression, but also weakly significant in the second). However, they do not lend support to expecting higher legislative activity associated with larger majority margins in the Senate (as the coefficients in both regressions were not statistically significant). Yet, we can expect higher legislative activity associated with greater quantities of vetoes (at a highly significant level as observed in the second regression). While counterintuitive, it again may be as increased usage of vetoes may also reflect increases of legislative productivity (given that presidents often increase veto usage when both chambers are opposed to their agenda). 

Yet, there exist some issues inherent to the data that we ought to consider as limitations. For starters, unobserved variables are always at play—for example, we saw House margin increases as solidly statistically significant on legislative activity prior to adding the veto variate into our model. Given the highly complex nature of policy-making and the political process (e.g. the influence of individual legislators or effectiveness of leadership), this limits our ability to completely isolate specific factors. Further, certain measurement issues exist with respect to our metrics. For example, it is difficult to standardize number of enacted bills as a function of legislative productivity—notably, bills have become longer over time (e.g. Dodd-Frank being ~2,000 pages), meaning that we could reach the same number of legal provisions without having more individual pieces of legislation enacted. In politics more broadly, given the shifting nature of institutions, it is difficult to maintain consistent metrics across decades. A final limitation to consider is the simplicity of our models themselves—we fit linear regressions onto potentially non-linear relationships. Those are three notable limitations among others that we ought to consider.

## Modeling set 3:
#### 1a)
gen republican = party_code == 200;

#### 1b)
gen Rpres1220 = ((Romney12 / (Romney12 + Obama12) + (Trump16 / (Trump16 + Clinton16)) + (Trump20 / (Trump20 + Biden20))) / 3);

#### 1c)
This new variable may be a useful estimate of legislator preferences because it represents the revealed political preferences of the district itself over three presidential cycles. In other words, it can reflect the ideological leanings and tendencies of voters over a period of time, which often influences/mirrors the way legislators behave. When compared to DW-NOMINATE scores, the utility of this metric is fairly straightforward — it reflects the broader leanings of the constituency itself as opposed to the voting behavior of the constituency’s representatives. In that vein, it is a different and more direct representation of what the voters of a particular district want (alongside spanning a longer period of time).

#### 1d)
The Democrat serving in the district with the highest average Republican presidential vote share is Carolyn Bourdeaux, whereas the Republican currently serving in the district with the lowest average Republican vote share is Mayra Flores. We get this using the code:
egen maxdem = max(cond(republican == 0, Rpres1220, .));
egen minrep = min(cond(republican == 1, Rpres1220, .));
list bioname if Rpres1220 == maxdem & republican == 0;
list bioname if Rpres1220 == minrep & republican == 1;

#### 2a)

```r
knitr::include_graphics("images/3-2a.png")
```

<div class="figure">
<img src="images/3-2a.png" alt="plot of chunk unnamed-chunk-21" width="2709" />
<p class="caption">plot of chunk unnamed-chunk-21</p>
</div>

#### 2b)
Looking at this graph, it appears that there is a positive correlation/relationship between the two given variables. In other words, as the average Republican presidential two-party vote share increases, the first dimension DW-NOMINATE score also appears to increase. This further suggests that districts with higher Republican vote share are more ideologically conservative. Yet, the relationship is not absolutely linear — there appears to be two clusters of points that do not align neatly under a linear model (likely reflecting the two parties).

#### 2c)
regress nominate_dim1 Rpres1220;

#### 2d)

```r
knitr::include_graphics("images/3-2d.png")
```

<div class="figure">
<img src="images/3-2d.png" alt="plot of chunk unnamed-chunk-22" width="2725" />
<p class="caption">plot of chunk unnamed-chunk-22</p>
</div>
Here, we first see an estimated intercept of -1.087994, which reflects the value we can expect for nominate_dim1 when Rpres1220 is zero. All this confirms is a quite intuitive/foregone conclusion — in districts with no Republican two-party presidential vote share, we would see a negative/quite liberal DW-NOMINATE score. We then see the estimated coefficient on the Republican presidential vote share variable (Rpres1220) of 2.396038, which represents the expected increase in the DW-NOMINATE score for a full-portion increase of (Rpres1220). This again affirms the prior foregone conclusion. With respect to whether this result is statistically significant — we see a p-value of approx. 0. This is far less than virtually every significance level, meaning that the probability of this result occurring by chance is essentially 0. Therefore, it is statistically significant. We can also assume that this result is substantively significant — the scope of the effect appears to account for an intuitively-large change, insofar as an increase of ~2.396 for said increase represents a massive ideological shift to conservatism.

#### 2e)
The results of the regression do in fact align with my interpretation of the graph in part b. In part b, we observed that there was a positive relationship between average Republican presidential two-party vote share and first dimension DW-NOMINATE. In this regression, we see further statistical proof for this — evidenced by both the intercept (which shows that when the vote share is low, the DW-NOMINATE is as well) and the estimated coefficient (which reflects that increases in vote share are associated with increases in DW-NOMINATE). Thus, all of these analyses show associations of greater ideological conservatism with higher Republican vote shares — confirming our interpretation.

#### 2f)

```r
knitr::include_graphics("images/3-2f.png")
```

<div class="figure">
<img src="images/3-2f.png" alt="plot of chunk unnamed-chunk-23" width="2683" />
<p class="caption">plot of chunk unnamed-chunk-23</p>
</div>

#### 3a)

```r
knitr::include_graphics("images/3-3a.png")
```

<div class="figure">
<img src="images/3-3a.png" alt="plot of chunk unnamed-chunk-24" width="2157" />
<p class="caption">plot of chunk unnamed-chunk-24</p>
</div>

#### 3b)
regress nominate_dim1 Rpres1220 republican;

#### 3c)

```r
knitr::include_graphics("images/3-3c.png")
```

<div class="figure">
<img src="images/3-3c.png" alt="plot of chunk unnamed-chunk-25" width="2053" />
<p class="caption">plot of chunk unnamed-chunk-25</p>
</div>
To begin, we first see an estimated intercept of -0.6035025, which is the value we can expect for nominate_dim1 when Rpres1220 is zero and the given member of Congress is not a Republican (republican == 0). This can be interpreted as a hypothetical that reflects the liberal expectation of the DW-NOMINATE score when the other two variables are held constant (of no vote share and non-Republican status). We then see the estimated coefficient on the Republican presidential vote share variable (Rpres1220) of 0.6448851, which represents the expected increase in the DW-NOMINATE score for a full-portion increase of (Rpres1220). In regards to the estimated coefficient on the republican variable, we see a value of 0.7135428 — a value that represents the increase of the DW-NOMINATE score based on being a Republican vs. not. Both of these coefficients are statistically significant — we see a p-value of approx. 0 on both. This is far less than virtually every significance level, meaning that the probability of these results occurring by chance are essentially 0. With regards to substantive significance — given the large association we see between the DW-NOMINATE score and both of the variables (an association so large that being a Republican or having said increase virtually ensures conservative ideological positions), our results are all substantively significant.

#### 3d)

```r
knitr::include_graphics("images/3-3d.png")
```

<div class="figure">
<img src="images/3-3d.png" alt="plot of chunk unnamed-chunk-26" width="2035" />
<p class="caption">plot of chunk unnamed-chunk-26</p>
</div>

#### 3e)
The coefficient estimate we see on the ‘Republican party indicator variable’ tells us that if a given member of Congress identifies as a member of the party, their associated first dimension DW-NOMINATE score observes an increase of approx. 0.7135428 (associated with greater ideological conservatism). With respect to what this tells us about parties in Congress — it shows us that the Republican Party is associated closely with conservatism, whereas the Democratic Party is associated more with liberalism. It also reflects a prevalent partisan divide between the two camps, which can also be observed within the data visualizations.

#### 4a)

```r
knitr::include_graphics("images/3-4a.png")
```

<div class="figure">
<img src="images/3-4a.png" alt="plot of chunk unnamed-chunk-27" width="2208" />
<p class="caption">plot of chunk unnamed-chunk-27</p>
</div>

#### 4b)
When we look at Rep. Adam Schiff’s position in the scatterplot in Q4a, we see that the x-axis (Republican pres. two-party vote share) approximates around 0.25 and the y-axis (first dimension DW-NOMINATE score) approximates around -0.35. I believe this reflects a relatively strong correspondence between my district’s ideology (as measured by presidential vote share) and the ideology of our Congressman’s roll call voting — both are distinctly liberal. This is furthered by Rep. Schiff’s point being rather close to the line of best fit that we formed through regression for Democrats. However, because the point is still above the line, it reflects that Rep. Schiff is voting slightly more moderately than expected for a district with such few Republican presidential votes.

#### 4c)
Based on parts a and b, I think my representative’s party pressures my representative to vote slightly more towards the party line. Again, the votes that Rep. Schiff casts seem to be ever-so-slightly more moderate than the constituency’s revealed electoral preference, and also towards the middle of the party cluster as a whole. However, it is difficult to tell whether this is the result of pressure or just Rep. Schiff believing in a ‘trustee’ model of representation, given the small size of the discrepancy.

## Modeling set 2:
#### 1a) 

```r
knitr::include_graphics("images/2-1a.png")
```

<div class="figure">
<img src="images/2-1a.png" alt="plot of chunk unnamed-chunk-28" width="1763" />
<p class="caption">plot of chunk unnamed-chunk-28</p>
</div>
I created multiple histograms here (density, frequency, and smoothed) as I was unsure which one the question was asking for. My presumption is that density and smoothed are the two better options — this is because the total observations for members of Congress not in the Ag. Committee (`Comm_Ag == 0`) is substantially larger. In general, I think regular frequency histograms are not that informative in cases where one of the groups being compared has a substantially larger sample size than the other (it creates a y-axis skew that makes intuitive comparison difficult). However, I am including them all to play it safe. The above code and visualizations were done after setting the delimiter as a semicolon (#delim ;). I also had some extra time this week so I read a couple of Stata tutorials on how to improve on data visualization — I thus decided to try the grstyle package to change the aesthetics. I think it makes everything look a little better.

#### 1b)
Describing the figures from part a, I will broadly refer to all at once as they reflect the same trend and distributions. What we can see are two distributions plotted next to each other in a histogram — one for the proportion of district agricultural workers in districts where the representative is on the Agricultural Committee (represented in blue), and one where they are not (represented in red). Both distributions are unimodal right-skewed, suggesting that the proportion of agricultural workers in either dichotomous type of district is not high. In other words, regardless of whether the M.C. is a part of the Agricultural Committee, agricultural workers comprise a minority of workers. However and interpretively, we can clearly see that there is a greater cluster of data points in lower bins (e.g. 0 to 0.05) for the ‘red’ distribution of districts without a member of Congress on the committee. Moreover, the ‘peak’/mode of the ‘blue’ distribution of districts with an M.C. on the committee seems to be further right along the x-axis. This goes to suggest that there is a potential association between having a higher proportion of district agricultural workers and a district having an M.C. on said committee.

#### 1c)
The line of code I executed to pull the summary statistics is:
by Comm_Ag, sort: summarize agri_workers, detail
The summary statistics I got are as follows:

```r
knitr::include_graphics("images/2-1c.png")
```

<div class="figure">
<img src="images/2-1c.png" alt="plot of chunk unnamed-chunk-29" width="1741" />
<p class="caption">plot of chunk unnamed-chunk-29</p>
</div>

#### 1d)
The line of code I executed to run the t-test is:
ttest agri_workers, by(Comm_Ag)
Note — I did not assume unequal variance because we pulled the variances for both of the categorical groups in part c. Because we got variances of .0007654 and .0005318, which are at a ratio of approx. 1.44 to 1, I assumed roughly equal variance. I generally assume equal variance if the variance of groups was less than 4:1. However, if this is not the correct way to approach variance assumptions when running two-sample t tests, please let me know. Nonetheless, this returns the following:

```r
knitr::include_graphics("images/2-1d.png")
```

<div class="figure">
<img src="images/2-1d.png" alt="plot of chunk unnamed-chunk-30" width="1080" />
<p class="caption">plot of chunk unnamed-chunk-30</p>
</div>
Here, we get a t-statistic of approx. -3.756. We can also clearly see that the two-tailed t-test returns a p-value of approx. 0.0002 (under the two-sided alternative hypothesis in the bottom-middle). Using an alpha of 0.05, this p-value is lower than said value. Therefore, the difference in means is statistically significant in this case. We reject the null hypothesis that there is no difference in means, and that it is unlikely the difference (between districts with/without a member of the ag. committee in relation to share of ag. workers in a district) occurs by chance.

We can take it a step further to cover all bases and run the test assuming unequal variance as well. We use the code: ttest agri_workers, by(Comm_Ag) unequal

```r
knitr::include_graphics("images/2-1d1.png")
```

<div class="figure">
<img src="images/2-1d1.png" alt="plot of chunk unnamed-chunk-31" width="1043" />
<p class="caption">plot of chunk unnamed-chunk-31</p>
</div>
Even under this, we get a t-statistic of approx. -3.266 and a two-sided p-value of 0.0019. Either way, this is still lower than the significance level of 0.05 and the difference in means remains statistically significant.

#### 1e)
This specific statistical test does support the ‘gains from trade’ theory to a strong degree — the evidence (that representatives from ‘agricultural labor-heavy’ districts are disproportionately represented on the Agricultural Committee) suggests a revealed preference of M.C.s to seek out committee seats that reflect their district’s unique farming interests (given the ability to ‘logroll’ and ‘pork’ for these interests vis-a-vis their inherent powers).

#### 2a)
Once again, I am including the multiple types of histograms to visualize said relationship.

```r
knitr::include_graphics("images/2-2a.png")
```

<div class="figure">
<img src="images/2-2a.png" alt="plot of chunk unnamed-chunk-32" width="1765" />
<p class="caption">plot of chunk unnamed-chunk-32</p>
</div>

#### 2b)
When we look at the figures from part a, we see unimodality / a slight right skew for both of the distributions (regardless of whether the member of Congress is on the Education & Labor Committee or not). Moreover, when focusing on the two figures that reflect relative frequency (the density and smoothed histograms), we see a near-equal shape between the two categories. On a descriptive sidebar: the variance of the distributions reflects that most districts tend to have a proportion of approx. 0.2 to 0.3 (the slight right skew) of enrolled college students — irrespective of their member’s status on said committee. Interpretively, I think this reflects a potential lack of preference outliers on this committee. Given the lack of apparent association between the ‘stakeholder’ (of college students) and disproportionate levels of ed/labor committee membership (which would reflect a preference outlier), I think this is a fair assessment to make.

#### 2c)
The line of code I executed to pull the summary statistics is:
by Comm_EdLab, sort: summarize univ_students, detail

```r
knitr::include_graphics("images/2-2c.png")
```

<div class="figure">
<img src="images/2-2c.png" alt="plot of chunk unnamed-chunk-33" width="1747" />
<p class="caption">plot of chunk unnamed-chunk-33</p>
</div>

#### 2d)
The line of code I executed to run the t-test is (once again under the assumption of equal variance):
ttest univ_students, by(Comm_EdLab)

```r
knitr::include_graphics("images/2-2d.png")
```

<div class="figure">
<img src="images/2-2d.png" alt="plot of chunk unnamed-chunk-34" width="1029" />
<p class="caption">plot of chunk unnamed-chunk-34</p>
</div>
Here, we get a t-statistic of approx. 0.323. We can also clearly see that the two-tailed t-test returns a p-value of approx. 0.747 (under the two-sided alternative hypothesis in the bottom-middle). Using an alpha of 0.05, this p-value is substantially higher than said value. Therefore, the difference in means is not statistically significant in this case. We fail to reject the null hypothesis that there is no difference in means, and it is likely the difference (between districts with/without a member of the ed/labor committee in relation to proportion of college students) occurs by chance.

Once again, we can cover all bases and run this test under the assumption of unequal variance as well. This is represented by the table below:

```r
knitr::include_graphics("images/2-2d1.png")
```

<div class="figure">
<img src="images/2-2d1.png" alt="plot of chunk unnamed-chunk-35" width="1013" />
<p class="caption">plot of chunk unnamed-chunk-35</p>
</div>
The t-statistic here is 0.3702, and our p-value under this remains substantially higher than our significance level (0.7123 > 0.05). The difference in means remains statistically insignificant.

#### 2e)
This specific statistical test does not support the ‘gains from trade’ theory  — the evidence above (that representatives from ‘college student-heavy’ districts are not disproportionately represented on the Education & Labor Committee) implies a lack of a revealed preference of M.C.s to seek out committee seats that reflect this specific interest (thus severing a logical conclusion of ‘logrolling’/‘porking’ for it).

#### 3a)
Because Rep. Adam Schiff (D-CA 30 and my hometown Congressman) is only on the Judiciary Committee and none of the demographic variables in the codebook seem to correspond to that, I am selecting the House Committee on Financial Services (coded as Comm_Fin) and the characteristic ‘Proportion of labor force employed in finance, insurance, and real estate’ (coded as fin_workers). I selected this given Los Angeles is one of the world’s financial capitals (we just beat out Shanghai this year in the GFCI!) and California has the highest number of financial firms among all U.S. states.

My justification as to why this characteristic is an appropriate measure is simple — if you have lots of people employed by the finance industry in your district, those people are likely to electorally pressure the given M.C. in ways that are advantageous to their future prospects (especially given the finance industry’s large lobbying power). It is in the same vein of thinking as question 1 of this exercise — if you want those finance industry votes and that war chest, you are likely to try to pander inside of the Financial Services Committee (given their vast control over regulations, banks, capital markets, oversight of monetary policy, and more).

#### 3b)
My expectation is that this committee will provide support for the gains of trade theory. This is because of the influence that I believe the finance industry has — in terms of the sheer quantity of people involved, the amount of money that circulates through it, and the cultural influence it’s held in America for so long. With that in mind, my intuition tells me that M.C.s likely know this, and seek to join the Financial Services committee to legislate accordingly.

#### 3c)
Below are the histograms for this set of variables:

```r
knitr::include_graphics("images/2-3c.png")
```

<div class="figure">
<img src="images/2-3c.png" alt="plot of chunk unnamed-chunk-36" width="1872" />
<p class="caption">plot of chunk unnamed-chunk-36</p>
</div>

#### 3d)
We again see unimodality / a slight right skew for both of the distributions (regardless of whether the member of Congress is on the Financial Services Committee or not). Moreover, when focusing on the two figures that reflect relative frequency, we see a similar shape between the two categories. Plus, the variance of the distributions reflects that most districts tend to have a proportion of approx. 0.025 to 0.1 (the slight right skew) of financial sector workers. However, it does appear that the values in the ‘red’ distribution (those without an M.C. on said committee) are affected by this positive skew more than those in the ‘blue’ distribution (those with). Thus, interpretively, I think this reflects both a potential preference outlier on this committee, but also a need for further data analysis (as the visuals do not give us a clear answer). I feel this ambiguity of association between the ‘stakeholder’ (of financial sector workers) and levels of financial services committee membership justify this conclusion. With respect to my district’s placement on the figures (represented by the dotted vertical line), I am admittedly a little surprised. I expected my district’s proportion to be higher, given the prevalence of financial firms in my community. However, I think this is not completely unexpected — LA’s Financial District, if I remember correctly, is closer to the center of my city than my neighborhood. Thus, it makes sense that financial sector workers may be concentrated in a nearby-yet-different congressional district.

#### 3e)
The line of code I executed to pull the summary statistics is:
by Comm_Fin, sort: summarize fin_workers, detail

```r
knitr::include_graphics("images/2-3e.png")
```

<div class="figure">
<img src="images/2-3e.png" alt="plot of chunk unnamed-chunk-37" width="1741" />
<p class="caption">plot of chunk unnamed-chunk-37</p>
</div>

#### 3f)
The line of code I executed to run the t-test is (once again under the assumption of equal variance):
ttest fin_workers, by(Comm_Fin)

```r
knitr::include_graphics("images/2-3f.png")
```

<div class="figure">
<img src="images/2-3f.png" alt="plot of chunk unnamed-chunk-38" width="1077" />
<p class="caption">plot of chunk unnamed-chunk-38</p>
</div>
Here, we get a t-statistic of approx. -2.8857. We can also clearly see that the two-tailed t-test returns a p-value of approx. 0.0041 (under the two-sided alternative hypothesis in the bottom-middle). Using an alpha of 0.05, this p-value is lower than said value. Therefore, the difference in means is statistically significant in this case. We reject the null hypothesis that there is no difference in means, and it is unlikely the difference (between districts with/without a member of the financial services committee in relation to proportion of financial sector workers) occurs by chance. 

Again, we can try this same test under an assumption of unequal variance by appending the word unequal to the end of the previous line of code. From that, we get:

```r
knitr::include_graphics("images/2-3f1.png")
```

<div class="figure">
<img src="images/2-3f1.png" alt="plot of chunk unnamed-chunk-39" width="1080" />
<p class="caption">plot of chunk unnamed-chunk-39</p>
</div>
Even under this, we get a t-statistic of approx. -2.1306 and a two-sided p-value of 0.0374. Either way, this is still lower than the significance level of 0.05 and the difference in means remains statistically significant.

#### 3g)
This specific statistical test does support the ‘gains from trade’ theory — the evidence (that representatives from ‘finance worker-heavy’ districts are disproportionately represented on the Financial Services Committee) suggests a revealed preference of M.C.s to seek out committee seats that reflect their district’s unique finance sector interests (given the ability to ‘logroll’ and ‘pork’ for these powerful interests vis-a-vis their powers).

#### 4a)
My Congressman, Rep. Adam Schiff, currently serves on one committee — the United States House Committee on the Judiciary. It is a permanent/standing committee that provides oversight over the federal courts, administrative agencies, and law enforcement. They are also notable for handling articles of impeachment (my Congressman’s specialty).

#### 4b)
If we are defining the ‘largest’ and ‘smallest’ committees by membership size, we can approach this problem in two ways. The first is by analyzing the data inside of the provided dataset directly. I did some digging on how to do for-loops in Stata and the following code seems to work (it does need to be run in the command line directly, as it uses local variables that don’t seem to translate to the do-file):

```r
knitr::include_graphics("images/2-4b.png")
```

<div class="figure">
<img src="images/2-4b.png" alt="plot of chunk unnamed-chunk-40" width="1792" />
<p class="caption">plot of chunk unnamed-chunk-40</p>
</div>
From this, we find that the committee with the most members is the Transportation Committee (with 67). The committee with the least members is the House Administration Committee (with 9). However, according to the internet (as of today), the largest is the Transportation Committee (with 75) and the smallest is the Ethics Committee (with 10). With respect to why this discrepancy exists — I have a few theories. Primarily, I assume the depth/scope/magnitude of issues that each committee deals with may necessitate more or less members. For example — transportation and infrastructure covers a pretty broad range of issues, whereas ethics probably deals with a lower quantity of issues on situational bases. In that vein, the former affects more people than the latter as well, requiring more policymakers to opine on the issues. Finally, I think there may be institutional factors at play. I imagine something like an ethics committee necessitating limited and specialized access into investigations, whereas transportation may not.

#### 4c)
I think the gains from trade theory increases how well some members of Congress represent their district, but on average (for any given district) and on net (for the entire nation) decrease the quality of policymaking. This is because Congressional resources are zero-sum in my mind, meaning that things like money, time, political capital, and attention are all involved in a web of trade-offs. In that vein, it seems to me that more elite/powerful members of Congress, under a system of constant ‘preference trading’, would likely dominate and center their preferences (i.e. equal players on unequal footing produce unequal outcomes, meaning any districts with a M.C. under the ‘power median’ receive an inequitable portion of said resources). Moreover, it seems like this model creates a tragedy of the commons, where each representative prioritizes pork barrel projects that bring them ‘clout’ from their respective districts over projects that benefit the nation (obstructing collective progress). 

## Pages index.html generator script

```r
knit("index.Rmd", output = "index.md")
```

```
## 
## 
## processing file: index.Rmd
```

```
## Error in parse_block(g[-1], g[1], params.src, markdown_mode): Duplicate chunk label 'setup', which has been used for the chunk:
## knitr::opts_chunk$set(echo = TRUE)
## library(markdown)
## library(knitr)
```

```r
markdownToHTML("index.md", "index.html")
```
