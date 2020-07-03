# Win/Loss Deviations in MLB Data w/ Pandas
In this project, you'll complete a data science project analyzing which Major League Baseball teams either outperformed or underperformed their *expected win total* for the given year.  This metric is used throughout the major league baseball season to measure how much luck might beinvolved in the team's win/loss total - which tells you whether to expect their winning or losing ways to continue or not.

We'll use baseball data a few times this semester.  Firstly, because I like baseball.  Secondly - perhaps more than any other sport, baseball lends itself exceedingly well to data collection and analysis - and the data has been collected remarkably well for over 100 years.

You should leverage `pandas` to perform this analysis - however I am not specifically providing requirements in *how* you perform your analysis (see below).

It's up to you whether to use a jupyter notebook or not for this.  You might want to use jupyter to explore the data and do some initial experimentation, but use a normal python script to do the full-scale processing - but it is up to you!

## Getting the Data
This project is set up with DVC, so do a `dvc pull` to get the data.  This command will download files to the `/data` directory - one file for each year of MLB data.

This data was downloaded from [retrosheets.org](https://www.retrosheet.org), which has a tremendous amount of data available to the public.

Each file in the `/data` directory is named GLYYYY.TXT, where YYYY is the year.  The file GL2015, for example, contains detailed information for each and every game played in the 2015 major league baseball season.  Each game occupies 1 row, and even though the file is named with a .TXT extension, it is indeed a CSV file format.

One file is a bit different - `glfields.txt` - this file describes which each column (even though they aren't labeled!) represents.  There are *many* columns - most of which we won't be interested in.

One thing to note:  The data files use (sometimes) non-intuitive notations for the team ID.  You can find information on team IDs [here](https://www.retrosheet.org/TeamIDs.htm).

## Calculating Expected Win/Loss
Expected Win/Loss percentage predicts the performance of a team based on the number of runs it scores and allows over a period of many games. The most common method of calculation is the **pythagorian winning percentage** - as defined as follows:

*Expected Winning Percentage* = *Runs Scored<sup>2</sup>* / (*Runs Scored<sup>2* + *Runs Allowed<sup>2*)

You can read a bit more [here](http://m.mlb.com/glossary/advanced-stats/pythagorean-winning-percentage).

Theoretically, if a team scores exactly the same number of runs over 10 games that it allows the opposing team to score - then the team might be expected to win half of it's games.  In reality, you could have a team win 9 out of the 10 games, and still score the same number of runs as their opponent - what matters is the score in *each* game.  

The theory behind why expected win loss rates are important is that overall, if a team goes 9-1 over 10 games, but scores the same number of runs as it's opponent, it probably got really lucky.  Over a long season (an MLB season typically is 162 games), their win loss percentage should converge to the expected percentage.  Teams that significantly outperform (or underperform) their expectations often have unique attributes, which might be difficult to identify.  The quest to identify (and predict) these attributes keeps many sports analytics data scientists employed in Major Leage Baseball!

## Analysis - Part 1 - Great Team Performances
For this project - you will calculate the expected win/loss pct for each an every team in the MLB, from the years 1871-2018.  List the top 50 teams performances - measured by how many games the team outperformed (more wins) their expected win loss in the given year.   Some notes:

- Game data - where each line represents a game - distinguishes between home and away teams.  Make sure that if you are calculating expected win/loss for the 1979 Pirates, you are including both their home and away games!
- The home team (column 6) won the game if column 10 (home team runs) is greater than column 9 (visiting team runs).
- Different years have different number of games.  In addition, some teams will play a few more or less games than others - do to rain outs or possible tie-breaker games.  Make sure you use the actual number of games played for a team when using expected winning percentage to calculate expected total wins.

## Analysis - Part 2 - Historically Lucky and Unlucky Teams
Do certain franchises consistently outperform or underform their expectations?  A lot of baseball fans think this is true - but our perspective is often skewed.  We can easily figure it out - by building off of part 1.

Calculate the *average* deviation (in games) between actual performance and expected performance across **all years for each team**, and list out the top 10 overperforming teams (actually won more games than expected), and top 10 underperforming teams (actually won less games than expected).  **Only include teams that played more than 10 years**.  

A few things to note here:

- Not all teams played in all years.  Most teams in the 1800's don't exist anymore, and there are teams currently in the MLB that are relatively new.  There are also many teams that have moved between cities and leagues, and will be identified differently.  When calculating the average deviation, make sure you aren't including years the team didn't exist!
- You can consult the team code files to determine which actual MLB team the codes refer too.  This is useful, but you do not need to report it in your output.

## Analysis - Part 3 - Home Field Advantage?
Now let's see if home field advantage helps teams outperform their expected win/loss totals.  Each game listed in each file has a home and away team listed. For this analysis, let's  compute the home team's actual win total (count how many times the home team won) against the home team's (all home teams!) expected total.  This really amounts to recalculating win/loss expectations specifically for the generic "home team".  Do this individually for each year between 1871-2018, and then calculate the average.  

### Important:  Expected and Actual Win Totals
When doing this analysis, your expected winning percentage for the home team is going to be around 53%.  Remember that although a MLB season is 162 games for each team, each year file contains thousands of games - because it represents all teams.  To make your expected win total, and actual win total resemble what you'd expect for an individual MLB team - normalize those to assume a 162 game schedule.  For example, if the expected win % is 0.53, and there are 2430 games played, then then instead of calculating expected win total as 1263.6, instead use 0.53 * 162 = 86.4.  You can easily calculate the actual win % for a given year as well - perhaps it is 0.51 for the home team.  Use 0.51 * 162 as the "actual" win total.

## Guidance
- Process each year (GLYYYY.TXT) one at a time.  In fact, I recommend doing the analysis just on one year first - and making sure that analysis is in a function or module that can be reused.  Once you have it right for one year, just call the same code over again for the other years.
- I suggest loading the GLYYYY.TXT file into a pandas data frame - potentially renaming columns to something more readable - and certainly filtering out columns not required for this analysis (you'll need columns 3, 6, 9, and 10).
- For a given year, you'll have a different set of teams (potentially).  Your first step in your analysis should be to enumerate the teams.  You can be certain that all teams will be listed in the home column (column 6) - use `pandas` to find the unique set.
- For a given year, and for a given team - you need to figure out how many runs that team scored and allowed.  To find out how many runs a team scored in a year:
  - Find the number of home team runs (column 10) the home team scored, when the home team (column 6) is the team you are analyzing.  
  - Find the number of visiting team runs (column 9) the visiting team scored, when the visiting team (column 3) is the team you are analyzing.  
  - Add these two values up - that's the team's total runs scored.
- Similarly, for a given year and given team, you need to calculate total runs **allowed**.  Do this just like the previous step - but you want to total up the number of visiting runs scored when the home team is the given team along with the number of home team runs scored when the *visiting* team is the given team. 
- With total runs scored/allowed, you can calculate the pythagorean exptected win total easily (see the formula described up top).  Keep in mind, the formula gives you a **winning percentage**, you'll need to multiply that by the number of games played - **(which is not the same each year!)** to get expected number of wins.
- To calculate total number of (actual) wins, you can count how many times the home team scored more runs than the visiting team  when the home team was the given team... and vice-versa.
- If you can accurately calculate expected wins and actual wins for one team, in one year, you can expand your analysis without changing much - so START SMALL!

## But... why aren't you giving me more instructions?
The last few weeks, I've been walking you through programs step by step, spelling out precisely how I'd like you to solve the problem.  This week (and most additional weeks), I'm expecting you to start structuring and designing your programs more indpendently.  This week's project requires you to do some thinking about how to access the data, how to analyze it, and even how to clean it.  There is quite a bit to think about - please ask for help if you need it.  Welcome to the real world of data science!

## But... I don't know anything about baseball!
I realize some of you are at a bit of a disadvantage if you are completely unfamiliar with baseball.  This is yet another reality of Data Science - you often **do not know much about the domain**.  If this project teaches you some skills in researching a domain enough to understand it's data, then it will be a great success - it's a valuable skill!

## Checking your work
Reading this section will spoil the fun of finding the answers to the analysis questions - but it's important for you to be able to feel confident your analysis is correct.

Let's look at some individual performances - your first step should be to create analysis code that matches up with these results:

| Year | Team | Exp. Wins | Actual Wins | Deviation|
|---|---|---|---|---|
| 1927 | New York Yankees (NYA)  |  112.5 | 110  | -2.5    |
| 1969 | New York Mets (NYN)  | 93.5  | 100  | 6.5  | 
| 1986 | New York Mets (NYN)  | 104.9  | 108  | 3.1  | 
| 2004 | Boston Red Sox (BOS)  | 97.9  | 98  | 0.1  |

The 1927 Yankees are widely considered one of the best teams of all time - it's interesting that they didn't win as many games as their run totals predict!  The 1969 Mets are called the "Miracle Mets", and the data seems to point to a bit of truth to that - they won a good amount more games than they probably deserved to!  Here's the top 5 team performances  of all time.

| Year | Team | Exp. Wins | Actual Wins | Deviation|
|---|---|---|---|---|
1905 |	Detroit Tigers (DET) |	64.4 |	79 |	14.6
2017 | San Diego Padres (SDN) |	57.3 |	71 |	13.7
2016 | Texas Rangers (TEX) |	81.9 |	95 |	13.1
2005 | Arizona Diamondbacks (ARI) |	64.5 |	77 |	12.5
1972 | New York Mets (NYN) |	71.0 |	83 |	12.0

Interestingly enough - none of these teams, aside from the 2016 Rangers, were very good.  Seems to suggest mediocre teams can be lucky too!

Once you've verified you can accurately calculate values for a given team, in a given year, then you can start calculating historical averages (Part 2). I'll give you a few:

| Team  | # of Years | Avg. Wins | Avg. Exp. Wins | Avg. Deviation|
|---|---|---|---|---|
| Seattle Mariners (SEA) |  42 | 75.0 | 74.8 | 0.2 |
| St. Louis Cardinals (SLN) | 127  | 79.1 | 79.6 | -0.5 |
| Montreal Expos (MON)  |  36 | 76.5 |76.1 | 0.4 |

If you take a look at all the teams, you'll notice that the average deviation is pretty small - especially for teams that have long histories.  The data seems to suggest there is no magical good or bad luck attached to teams over the years... which probably makes sense.

For Home Field advantage (Part 3), you have a bit more work to do - as you need to recompute expected win totals - this time for home/away as opposed to individual teams.  Here's a sampling so you can compare with your results.  Remember, these numbers use total home team and away team runs over the entire year to calculate an exp winning percentage, but for year assume there are 162 games and calculate expected by multiplying the expected winning percentage by 162 and actual wins by multiplying actual winning percentage by 162.

|Year | Team | # Actual Wins | # Expected Wins | Deviation |
|---|---|---|---|---|
| 1973| Home | 85.9| 83.4| 2.44|
| 2000| Away | 74.5 | 78.4 | -3.9 |
| 2006| Home | 88.5| 84.4| 4.1|
| 2015| Away | 74.2| 77.4 | -3.2

On average, the home team has a winning percentage of about 55%, which over the course of 162 games would lead to 88.5 wins.  That's a significant home field advantages - but interestingly, the expected winning percentage is less - it's about 53%.  That amounts to 2 more wins each year above what would be expected - which is actually quite significant.   

This means home field advantages **is not** entirely explained by runs scored - teams really do tend to benefit from other features (maybe the fans, maybe getting the bat last, etc.) that helps them win games.  For baseball fans, this probably isn't that surprising - but it's nice when the data confirms your intuition!
