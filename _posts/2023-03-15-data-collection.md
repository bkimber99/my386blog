---
layout: post
title:  "Pay to Win?"
author: Bryce Kimber
description: Does a larger budget for sports really mean more wins? This post shows how to collect the data to examine this question for college football.
image: /assets/images/2023-03-15-data-collection/money.png
---

## Introduction

For many years now those that follow sports know that in professional sports the teams with the most money can "buy" the players that they want to help them win. This is how professional sports work, and thats one of the biggest differences that separates it from college sports. That is until the new nationwide NCAA NIL rule changes, but we're not going to get into that side of things in this post.

Even though college sports doesn't envolve schools paying athletes (at least according to the rules) it still seems sometimes like the schools with more money seem to win more. They hire good coaches and have nice facilities that attrict higher calibur athletes. But does this actually mean that they win more? To me it seems obvious that it does, but one recent team shows me that it may not be true, which is also why I decided to dive into this topic further.

The example team is the Texas Longhorns football team. As of last year the Texas football program had the largest football budget of any college football team in the nation, and according to [CollegeRaptor](https://www.collegeraptor.com/college-rankings/details/TopRevenue/) it was by 10 million... Yet their record last year was 8-5, or a 61.5% win percentage, or a D-...

## Let's get the Data

I found the first part of my data from a public github repository called tidytuesday on the rfordatascience github page. The data was from [Week 13](https://github.com/rfordatascience/tidytuesday/tree/master/data/2022/2022-03-29) in this project. This is a huge dataset and I definitely don't need all of it, but we'll clean it later.

The second part of the data that I needed is the record for each team for each year that I am looking at. I was able to find that informaiton from [this website](https://www.teamrankings.com/ncf/trends/win_trends/). I decided it was okay to scrape this data because it was publicly available without needing to create an account. However the site does provide a way to contact the creator of the website if needed.

Once I had these peices I was able to start wrangling things with python. The first large data set I downloaded in the form of a .csv file which I then read into a dataframe after importing the required packages.
```
sports = pd.read_csv("sports.csv")
```
After this I located the data that I needed and slimmed my dataframe down to only include that part of the giant table. You'll notice that I created two smaller dataframes from the large one. This will allow me to more easily join the record data later. After the record data is joined I can merge these two smaller datasets again. I also may pivot the dataframe and make each year and record column values.
```
sports = sports.loc[sports['classification_code'] == 1]
sports = sports.loc[sports['sports'] == 'Football']
sports_2019 = sports.loc[sports['year'] == 2019]
sports_2019.drop(['year','unitid','city_txt','state_cd','zip_text','classification_other','ef_female_count','ef_total_count','sector_cd','sector_name','sportscode','partic_women','partic_coed_men','partic_coed_women','sum_partic_men','sum_partic_women','rev_women','total_rev_menwomen','exp_women'],axis=1)
sports_2019.reset_index()
sports_2018 = sports.loc[sports['year'] == 2018]
sports_2018.drop(['year','unitid','city_txt','state_cd','zip_text','classification_other','ef_female_count','ef_total_count','sector_cd','sector_name','sportscode','partic_women','partic_coed_men','partic_coed_women','sum_partic_men','sum_partic_women','rev_women','total_rev_menwomen','exp_women'],axis=1)
sports_2018.reset_index()
```

Getting the wins data was a little trickier. Utilizing beautiful soup I was able to scrape the table from the page, and that was the easy part. Getting just the two columns of information I needed was tricky but I was able to do it out of trial and error by changing the coordinates in my for loop. I repeated this next step for 2019 through 2017.
```
url = "https://www.teamrankings.com/ncf/trends/win_trends/?range=yearly_2019"
response = requests.get(url)
soup = BeautifulSoup(response.content, "html.parser")
table = soup.find("tbody")
rows = table.find_all("tr")
name = []
win_perc = []
for row in table.find_all("tr")[0:]:
    team_name = row.find_all("td")[0].text.strip()
    win_percentage = row.find_all("td")[2].text.strip()
    name.append(team_name)
    win_perc.append(win_percentage)
data = {"Team": name, "Win Percentage": win_perc}
wins_2019 = pd.DataFrame(data)
```
## Conclusion

Now that I have these pieces of information I just need to put them all together. This is something that I have yet to do but will include the process in my next post. It won't be too hard, and I'll just need to make sure my smaller dataframes are sorted in the same order and then join them using the pandas.DataFrame.join() method. Feel free to follow along with my project here on this website and in [this](https://github.com/bkimber99/pay2win) github repository.