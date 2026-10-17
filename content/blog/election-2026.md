---
title: Linear Regression Model for 2026 Elections
author: 'Jayden Li'
date: '2026-10-16'
params:
  math: true
---

## Introduction

**TLDR: Generic ballot D+8.53, House D236-R199.** [Jump to results](#results)

We will create a simple model to predict the *generic ballot* for the 2026 US midterm elections. Much has been said about polls and how they're unreliable; overall, polls are pretty good and are themselves evolving into models in of themselves (sampling, weighting, demographics, response rate, etc). In recent memory (to my knowledge) polls have been wrong in *both* directions (D-biased meaning overestimating democrats, and vice versa):
<details>
<summary>Poll bias</summary>

 - **2025**: very R-biased (NJ/VA gubernatorial). Poll aggregators had Sherrill (D) up only [~4.7pp](https://en.wikipedia.org/wiki/2025_New_Jersey_gubernatorial_election#Polling_3) (causing some to think she might actually lose). She actually won by ~14.4pp, a difference of almost 10pp! The error was around 5pp in [Virginia](https://en.wikipedia.org/wiki/2025_Virginia_gubernatorial_election#Polling_3) in favor of Rs as well.
 - **2024**: slightly D-biased. [National polls](https://en.wikipedia.org/wiki/Nationwide_opinion_polling_for_the_2024_United_States_presidential_election) had Harris up slightly in the popular vote, which Trump won by 1pp in the end for ~2pp poll error. Overall, there was a similar error in statewide presidential and congressional polls as well.
 - **2022**: no significant bias. [RCP average](https://www.realclearpolling.com/polls/state-of-the-union/2022/generic-congressional-vote) of R+2 for Rs was almost exactly the R+2.7 actual popular vote. 538 was around [1.5pp off](https://en.wikipedia.org/wiki/2022_United_States_House_of_Representatives_election_ratings#Generic_ballot_polls) in favor of democrats.
 - **2020**: very D-biased.
</details>

The point is that polls can miss in both directions, and the variance in generic ballot polling this year has been quite high: they show a margin of anywhere from [D+2 to D+15](https://www.nytimes.com/interactive/polls/congressional-vote-2026.html) just in late September-early October when I am writing this.

So, it may be interesting to see a model that *not* take polls into account. We will use some (but not all) of the following data points (links are for most recent elections in 2025/2026):
 - [New Jersey](https://en.wikipedia.org/wiki/2025_New_Jersey_gubernatorial_election) and [Virginia](https://en.wikipedia.org/wiki/2025_Virginia_gubernatorial_election) gubernatorial elections.
 - Washington primaries. WA uses top-two primaries, where all candidates (regardless of party) are listed on the same ballot in the primary, and the top-two finishers advance to the general election in November. We can find partisanship by adding up the votes received by D and R candidates in each race. Washington's state-wide elections are held in presidential years, so use [US House primaries](https://en.wikipedia.org/wiki/2026_United_States_House_of_Representatives_elections_in_Washington) instead.
 - [Special elections](https://en.wikipedia.org/wiki/Category:Special_elections_to_the_119th_United_States_Congress) to the current House of Representatives (triggered when a member resigns or dies).
 - Presidential approval rating. I didn't want to use polls at all, but it improved the model significantly. Approval rating measures the environment instead of polling the results directly, so I think we can use it.

The history of primaries in Washington and California is very interesting. Open the drop-down below for more information.
<details>
<summary>Note on Washington and California primaries</summary>

Washington used the *blanket* primary between 1935 and 2003, where all candidates are ranked on the same ballot and the top performers of *each party* advance to the general election (so there is one candidate from each party in the general election). This is different from the *top-two* primary used in WA and CA now, where the top two performers regardless of party, advance.

California adopted the blanket primary by 1996 (by Proposition 198), and it was used in the 1998 statewide elections. In 2000, the Supreme Court [found the blanket primary unconstitutional](https://en.wikipedia.org/wiki/California_Democratic_Party_v._Jones), and California reverted to party primaries. Nonetheless, Washington used the same blanket primary in 2000 and 2002, before it too was struck down ahead of the 2004 elections. In 2004, Washington used party primaries. Also in 2004, Washington adopted the top-two primary by Initiative 872, but it was struck down as unconstitutional by a US District Court. In 2006, Washington again used party primaries. In 2008, though, the Supreme Court [upheld the constitutionality of the top-two primary](https://en.wikipedia.org/wiki/Washington_State_Grange_v._Washington_State_Republican_Party) in Washington, and 2008 was the first election conducted with the top-two system. Washington has used the top-two primary for all elections since then.

In 2010, California adopted the top-two primary with Proposition 14. It was used in 2012 elections and has been used since then.
</details>

I will not be using:
 - California primaries. California only used a top-two or blanket primary system in 1998 and since 2012, so there is not enough data.
 - [Primary elections](https://en.wikipedia.org/wiki/2026_United_States_electoral_calendar) in other states by considering *primary composition*: voters in D primary minus voters in R primary. Unlike top-two primaries, party primaries with D/R candidates on separate ballots only sample those leaning/supporting a certain party. (Some states have open primaries where all voters can vote and there is no "voter registration," some have semi-closed or closed primaries where primary voting eligibility is limited by registration.) Additionally, primary composition is skewed by the competitiveness of the primary: if there's only one candidate who is a heavy favorite, of course turnout will be lower. Primary composition is good at measuring *enthusiasm* in a certain party's base, though, and may be predictive of the November electorate's party affiliation.
 - NJ and VA legislative elections. Too much noise

Some forecasters already include these metrics. [RaceToTheWH](https://www.racetothewh.com/house/methodology) uses CA/WA primaries, congressional special elections and NJ/VA gubernatorial elections, for example.

Our model is quite simple and will have a number of limitations. We discuss them at the end. At the end, I'll also include a simple and dumb prediction for each House seat.

You can view the code and dataset [here](https://github.com/glolichen/election-2026).

<!-- Note: you can verify the date of publication by checking the [Wayback Machine](). I made an archive on October 16.[^1] -->
<!---->
<!-- [^1]: Yeah, I forgot to update the publication date from "10/5" the first time. -->

## Dataset

The full dataset will be:
 - Special elections to current congress.
 - Virginia gubernatorial election result.
 - New Jersey gubernatorial election result.
 - Washington congressional primary result.
 - Presidential approval rating.

For the first four, it makes more sense to compare the result to the state or district's known partisanship rather than the raw result. This gives us a standard national baseline accounting (partially) for regional differences. We find the partisanship (PVI)) by taking a weighted average of the last two presidential elections in that state/district.

Let \(A\) be the margin (D percentage minus R percentage) of the last presidential election and \(B\) be the margin of the one before that. (So in 2026, 2024 and 2020, respectively). The PVI of any district/state is:
\[
    \text{PVI}=\alpha A+\beta B
\]
For congressional districts we use 3:1 weighting \(\alpha=3/4,\beta=1/4\), but for NJ and VA we use 2:1 weighting \(\alpha=2/3,\beta=1/3\) due to large swings in the 2024 election.

This is similar to how [Cook PVI](https://en.wikipedia.org/wiki/Cook_Partisan_Voting_Index) is calculated.

Unfortunately I was too lazy to do this for elections before 2008 (where I had to gather results manually) so for those the PVI is just the presidential margin from the last election.

Then, for each election, we calculate the swing by subtracting the district/state's PVI from the margin. We use the convention that positive values are Democratic and negative values are Republican. For example, the swing in the [TN District 7](https://en.wikipedia.org/wiki/2025_Tennessee%27s_7th_congressional_district_special_election) special election in December 2025 was D+11.25, or \(+11.25\).

For VA and NJ gubernatorial elections, there is a single swing. For special elections and the Washington congressional primaries, we take the swing of each individual race and take the [trimmed mean](https://en.wikipedia.org/wiki/Truncated_mean) by \(10\%\).

Important note for Washington primaries: Washington held a party primary in 2006 after its system was struck down (I talked about this in the history of WA/CA primaries). Because 2006 is too important a year to leave out (second term, unpopular president, ...?), I added up the D and R vote shares anyway like it were a top-two/blanket primary, even if they appeared on separate ballots. The Washington Secretary of State's website appears to do this too. 

I used presidential approval rating from [Gallup](https://news.gallup.com/poll/116677/presidential-approval-ratings-gallup-historical-statistics-trends.aspx). I used value from the last poll before the midterm election. They polled this since 1945 but stopped in 2026, so I used [Nate Silver's average](https://www.natesilver.net/p/trump-approval-ratings-nate-silver-bulletin) for 2026 instead. Yes, it's not ideal.

Let \(R\) be the president's approval rating. Assuming everyone else disapproves, the net approval rating is \(N=R-(100-R)=2R-100\). Interpret this as a partisan value by multiplying it by \(1\) for Republicans and \(-1\) for Democrats. For example, Joe Biden's approval rating on November 1, 2022 was 40 for a signal of R+20 or \(-20\). We could half these values but they would be equivalent in the regression.

We will go back to the 1994 election for a total of 8 elections in the "training set" (up to 2022). I didn't want to go so far back at first but we really just need more data, even if politics has changed a lot since 1994.

## Model

Let the predictors \(S,V,N,W,A\) be special election swing, VA gubernatorial swing, NJ gubernatorial swing, WA congressional primary swing and presidential approval rating, respectively. Let \(Y\) be the midterm generic congressional ballot, which we are trying to predict. For convenience, let \(X_1=S,X_2=V,\ldots\). A multivariable linear regression for \(Y\) is:
\[
    Y
    =X_0+B_1S+B_2V+B_3N+B_4W+B_5A
    =X_0+\sum_{i=1}^5 B_iX_i
\]
Note that the predictors are highly correlated with each other. Having information on one of these gives a lot of information on the others, as it indicates something about the national environment. These predictors are highly *collinear* and we need to correct for this multicollinearity.

We use [ridge regression](https://www.ibm.com/think/topics/ridge-regression), which is commonly used to correct for multicollinearity. It is an ordinary least squares regression with a penalty term on the size of the coefficients, in effect forcing the coefficients \(B_i\)'s to be smaller. This increases numerical stability and reduces the risk of overfitting.

We don't know which of the signals above are the most predictive so we will train a model on each nonempty subset of the predictors (and ignore the others). There are \(2^5-1=31\) such subsets of \(\{ S,V,N,W,A \}\) and thus 31 models, each trained on a different selection of predictors.

We want to know how well each subset/model performs with leave one out cross-validation (LOOCV). We train the model **excluding a certain year**. It is important to do this to avoid "testing on the training set." Then we evaluate the model on the left out year and compare its output to the actual House popular vote. We have the errors \(E_1,\ldots,E_8\), each representing the error for each year between 1994 and 2022.

The root mean square error (\(\text{RMSE}\)) and mean absolute error (\(\text{MAE}\)) are:
\[
    \text{RMSE}
    =\sqrt{\frac 18 \sum_{i=1}^8 E_i^2}
    \qquad \text{MAE}
    =\frac 18\sum_{i=1}^8 |E_i|
\]
Then, we can find the best subset of predictors to use by comparing \(\text{RMSE}\) and \(\text{MAE}\). We prefer models with lower errors.

## Results

The columns on the left show whether each predictor was used, followed by \(Y\), the predicted 2026 generic ballot, and finally the \(\text{RMSE}\) and \(\text{MAE}\) error values. The best models were:

|Specials|VA|NJ|WA|Pres. App.|\(Y\)|\(\text{RMSE}\)|\(\text{MAE}\)|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Y| | |Y|Y|**D+8.53**|1.079|0.944|
|Y| |Y|Y|Y|**D+8.35**|1.279|1.178|
|Y|Y| |Y|Y|**D+8.57**|1.380|1.201|
| | | |Y|Y|**D+5.95**|1.770|1.430|
|Y| | | |Y|**D+9.97**|2.024|1.515|

The Specials+WA+Approval model had the best \(\text{RMSE}\) and the best \(\text{MAE}\).

Interestingly, the best model does not use VA or NJ gubernatorial results. It makes sense as (when politics were not as polarized) there are huge localized effects, such as [Chris Christie's landslide reelection in 2014 in NJ](https://en.wikipedia.org/wiki/2013_New_Jersey_gubernatorial_election). But it is encouraging that adding each individually to the Specials+WA+Approval model gives three models give results within 0.22 points of each other, and with good error values as well.

Presidential approval rating is essential here, hence I was willing to bend/eliminate the "no polling" stipulation to add it. The Washington primary is also highly predictive, confirming a fairly well known fact.

**Final prediction: D+8.53.**

<details>
<summary>Fun and bad models</summary>

The most democratic, most republican and worst (highest error) models were:
|Specials|VA|NJ|WA|Pres. App.|\(Y\)|\(\text{RMSE}\)|\(\text{MAE}\)|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Y| | | |Y|D+9.97|2.024|1.515|
| | |Y| | |R+0.07|6.938|5.891|
|Y|Y|Y|Y| |D+5.86|11.716|7.751|

The D+9.97 model isn't even that bad error wise (and is in the top 5 above). The other two are worse, though. Yes, the Specials+VA+NJ+WA model has the worst \(\text{RMSE}\) and the worst \(\text{MAE}\), and by a good margin as well.
</details>

<details>
<summary>All models</summary>

I am too lazy to enter all 31 into a table, so here's a raw dump.
```
S+WA+A: D+8.53, RMSE 1.079, MAE 0.944
S+NJ+WA+A: D+8.35, RMSE 1.279, MAE 1.178
S+VA+WA+A: D+8.57, RMSE 1.380, MAE 1.201
WA+A: D+5.95, RMSE 1.770, MAE 1.430
S+A: D+9.97, RMSE 2.024, MAE 1.515
VA+WA+A: D+5.95, RMSE 2.143, MAE 1.809
S+VA+A: D+9.41, RMSE 2.399, MAE 1.985
NJ+WA+A: D+5.56, RMSE 2.516, MAE 2.118
NJ+VA+WA+A: D+5.64, RMSE 3.201, MAE 2.646
S+NJ+A: D+9.46, RMSE 3.592, MAE 2.944
S+NJ+VA+WA+A: D+8.27, RMSE 3.683, MAE 2.598
S: D+8.36, RMSE 4.283, MAE 3.746
S+NJ+WA: D+6.26, RMSE 4.524, MAE 4.126
VA+A: D+4.99, RMSE 4.728, MAE 4.374
S+VA: D+7.51, RMSE 5.086, MAE 4.448
S+WA: D+6.21, RMSE 5.131, MAE 4.637
S+NJ: D+7.66, RMSE 5.168, MAE 4.576
S+VA+WA: D+5.87, RMSE 5.193, MAE 4.693
S+NJ+VA+A: D+8.97, RMSE 5.196, MAE 3.530
NJ+VA+A: D+4.54, RMSE 5.570, MAE 4.771
NJ+A: D+2.95, RMSE 5.911, MAE 4.884
A: D+2.41, RMSE 6.013, MAE 5.376
WA: D+2.95, RMSE 6.778, MAE 5.504
NJ: R+0.07, RMSE 6.938, MAE 5.891
S+NJ+VA: D+7.04, RMSE 7.035, MAE 5.655
NJ+WA: D+3.01, RMSE 7.066, MAE 6.219
NJ+VA+WA: D+2.56, RMSE 7.136, MAE 6.094
NJ+VA: D+1.03, RMSE 7.196, MAE 5.999
VA+WA: D+2.54, RMSE 7.356, MAE 6.336
VA: D+0.84, RMSE 7.468, MAE 6.353
S+NJ+VA+WA: D+5.86, RMSE 11.716, MAE 7.751
```

</details>

## Limitations

I cannot possibly claim that this is a model as good as those available now. Our dataset (past year election results) is affected by the national environment (a signal we try to extract) and a number of other factors, including:
 - Localized effects: we are looking at a fairly limited sample of the electorate here. We have NJ, VA, CA, WA and some special elections across the country. What if there are factors specific to a certain area that may not be present or as significant nationally? For example, Spanberger ran up the margin because of how bad her opponent was and Trump's cuts to the national government having an outsized impact to Northern Virginia, where many federal employees live. We are assuming a *uniform* national swing, but this of course isn't true.
 - Candidate quality: self explanatory, better/worse candidates will cause the margin/result of an election to change in a way that may not fully translate to other races. (This is not *fully* localized, though; for example in 2022 and 2024 Rs nominated a number of fairly bad candidates and lost seats that should have been winnable.)
 - Off-year electorate: in recent years, it's becoming the case that high-propensity (likely to vote) voters are disproportionately democratic. They are more likely to vote in primaries and non-midterm or presidential elections like 2025. This is especially exaggerated in lesser-known/lower-profile contests like [2026 GA PSC](https://en.wikipedia.org/wiki/2026_Georgia_Public_Service_Commission_election) or [WI Supreme Court](https://en.wikipedia.org/wiki/2026_Wisconsin_Supreme_Court_election) elections. This may be a reason why polls overestimate Ds in presidential elections but often underestimate them in midterm or odd-year elections.
 - Lack of polling or fundamentals: kind of self explanatory. We did use presidential approval rating though. Public opinion changes a lot, and the November general election may be held under an electorate whose opinion has changed significantly since the previous elections held a few months or a year out. You need polling to measure such changes, and we are not doing that.

Of course, the biggest limitation: <u>**historical trends hold, until they don't**</u>. This model is, by its design, entirely based on historical data. The 2024 Washington primary [suggested](https://split-ticket.org/2024/08/22/a-very-detailed-examination-of-the-washington-primary/) a 2% improvement for Democrats from 2020, which obviously did not happen. I would not be surprised at all if I am somewhat or totally wrong, as the only constant in politics is change.

## Seat-by-Seat Predictions

Here is a uniform-swing prediction of each seat in the House of Representatives (post-redistricting) using nothing but generic ballot (predicted as D+8.53) and [Cook PVI from Wikipedia](https://en.wikipedia.org/wiki/Cook_Partisan_Voting_Index). I am comparing the generic ballot to a district's partisanship, and ignoring everything like candidate quality, localized swings, etc, so this will almost certainly be wrong!

With the same sign convention as throughout the article (\(+\) for D, \(-\) for R): let \(C\) be the Cook PVI of a seat and let \(N_D,N_R\) be the "national popular vote" share for Democrats and Republicans, respectively (for president when calculating CPVI, but national House generic ballot for our purposes). Let \(D,R\) be the Democratic and Republican vote share of this district. Assuming there are only votes for Democrats and Republicans: we have \(R=1-D\). The generic ballot margin is \(M_N=N_D-N_R\).
\[
D=N_D+C \qquad R=N_R-C
\]
Let \(M_\text{seat}\) be the margin of this seat:
\[
M_\text{seat}
=D-R
=(N_D+C)-(N_R+C)
=(N_D-N_R)+2C
=M_N+2C
\]
The Democrats will win a seat if and only if \(M_\text{seat}>0\) (suppose there are no ties):
\[
\text{D Win}
\iff M_\text{seat}=M_N+2C>0
\iff M_N>-2C
\]
For example, suppose a seat has CPVI R+4, or \(-4\). Then it takes a national generic ballot of greater (more Democratic) than \(-(-4)=+8\), i.e. D+8 or more.

The predicted margin \(M\) is a seat's CPVI (\(+\) for Democratic, \(-\) for Republican) plus \(+8.53\). Sort into tilt/lean/likely/safe as follows:
 - \(0 < M \leq 1\): Tilt.
 - \(1 < M \leq 5\): Lean.
 - \(5 < M \leq 12\): Likely.
 - \(M > 12\): Safe.

![House predictions](/images/2026-house.png)

I think this is actually somewhat reasonable. There are some seats that I disagree with (NC-01, NY-01, OH-07 because of the Max Miller scandal, OH-09, OH-10, TX-15), but overall, I think it's as good as it gets for a uniform swing model. You can compare it to the [RaceToTheWH](https://www.racetothewh.com/house) and [Split Ticket](https://www.theargumentmag.com/p/split-ticket-2026-midterms-model) forecasts. The predictions are not too dissimilar.

<details>
<summary>Seat flips</summary>

<u>Underlined</u> incumbents retiring. Seats marked by * affected by redistricting.

|Seat|Incumbent|Before|After|Margin|
|:-:|:-:|:-:|:-:|:-:|
|AL-2 *|Figures|D|R|R+5|
|AZ-1|<u>Schweikert</u>|R|D|D+6|
|AZ-6|Ciscomani|R|D|D+8|
|CA-1 *|Gallagher|R|D|D+22|
|CA-6 *|Kiley|I(R)|D|D+20|
|CA-22 *|Valadao|R|D|D+10|
|CA-41 *|<u>Calvert</u>¹|R|D|D+26|
|CA-48 *|<u>Issa</u>|R|D|D+12|
|CO-8|Evans|R|D|D+8|
|FL-9 *|Soto|D|R|R+7|
|IA-1|Miller-Meeks|R|D|D+0.5|
|IA-2|<u>Hinson</u>|R|D|D+0.5|
|IA-3|Nunn|R|D|D+4|
|LA-6 *|Fields|D|R|R+23|
|MI-4|Huizenga|R|D|D+2|
|MI-7|Barrett|R|D|D+8|
|MI-10|<u>James</u>|R|D|D+2|
|MO-2|Wagner|R|D|D+0.5|
|NE-2|<u>Bacon</u>|R|D|D+14|
|NJ-7|Kean|R|D|D+8|
|NY-1|LaLota|R|D|D+0.5|
|NY-17|Lawler|R|D|D+10|
|NC-1 *|Davis|D|R|R+1|
|OH-9 *|Kaptur|D|R|R+1|
|OH-10 *|Turner|R|D|D+0.5|
|PA-1|Fitzpatrick|R|D|D+10|
|PA-7|Mackenzie|R|D|D+6|
|PA-8|Bresnahan|R|D|D+0.5|
|PA-10|Perry|R|D|D+2|
|TN-9 *|<u>Cohen</u>|D|R|R+9|
|TX-9 *|*None*|D|R|R+9|
|TX-32 *|*None*|D|R|R+7|
|UT-1 *|*None*|R|D|D+32|
|VA-1|Wittman|R|D|D+2|
|VA-2|Kiggans|R|D|D+8|
|WI-1|Steil|R|D|D+4|
|WI-3|Van Orden|R|D|D+2|


¹: Running in CA-40.

Margins are not precise as I only got PVI without decimal places. I probably got the redistricting mixed up somewhere, especially California.
</details>

Take these this with a grain of salt. Uniform national swing like this is not a good way of predicting the House as the [limitations](#limitations), specifically the localized effects, are even more pronounced.

This is even worse at predicting the Senate, as candidate quality, localized effects, state-specific factors, etc are all have a greater effect on statewide races than House races. Here is a prediction anyway.
<details>
<summary>Senate</summary>

![Senate predictions](/images/2026-senate.png)

This is actually consistent with where analysis of the Senate race is right now. The Lean R states of Iowa, Texas, Alaska, Ohio are the current battlegrounds, and a good/bad candidate and some localized trends give Democrats a chance to win. I'm would not rate New Hampshire and especially Maine as Safe D, but the model only considers generic ballot and in that case it would make sense.
</details>

## Sources
- WWU, [1992 Presidential Election by Congressional District](https://library.cqpress.com/cqalmanac/document.php?id=cqal93-844-25158-1103932#_).
- WWU, [1996 Presidential Election by Congressional District](https://sigcfe.maps.arcgis.com/apps/mapviewer/index.html?panel=gallery&suggestField=true&layers=e5a43dafa46f4d4585611c4d51b5f5cd).
- WWU, [2000 Presidential Election by Congressional District](https://wwu.maps.arcgis.com/apps/instant/minimalist/index.html?appid=ec3ad6a61b334fb6b858e1f4e791c583).
- WWU, [2004 Presidential Election by Congressional District](https://wwu.maps.arcgis.com/apps/instant/minimalist/index.html?appid=8f02b1ba3b3043f4bd9d5f534df128dd).
- The Downballot, [Presidential Elections by Congressional District since 2008](https://www.the-downballot.com/p/the-downballots-calculations-of-presidential).
- Washington Secretary of State, [History of WA State Primaries](https://www.sos.wa.gov/elections/data-research/history-laws-and-litigation/history-washington-state-primary).
- Washington Secretary of State, [Past Election Results](https://www.sos.wa.gov/elections/data-research/election-data-and-maps/election-results-and-voters-pamphlets).
- Ballotpedia, [Congressional Special Elections since 1987](https://ballotpedia.org/U.S._Congress_special_elections_historical_data,_1987-2024).
- Gallup, [Presidential Approval Ratings Before 2026](https://news.gallup.com/poll/116677/presidential-approval-ratings-gallup-historical-statistics-trends.aspx).
- Nate Silver, [Trump Approval Rating 2026](https://www.natesilver.net/p/trump-approval-ratings-nate-silver-bulletin).
- Too much from Wikipedia to count, but [VA elections](https://en.wikipedia.org/wiki/Elections_in_Virginia) and [NJ elections](https://en.wikipedia.org/wiki/Elections_in_New_Jersey).

(WWU = Western Washington University. Something about Washington, I guess.)

