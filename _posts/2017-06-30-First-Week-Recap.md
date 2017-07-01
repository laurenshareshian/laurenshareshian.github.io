---
layout: post
Title: First Week Recap of Metis Data Science Bootcamp
---

## General Thoughts
Although the first week of the bootcamp was fast paced, I was pleasantly surprised to find that I knew more than I thought! This comfort was due to significant studying that I did independently in preparation for the bootcamp. In particular, I used the following resources that I highly recommend:

* I took Python & data science courses through the [UCSD Advanced Analytics program](https://extension.ucsd.edu/courses-and-programs/data-mining-for-advanced-analytics).
 
* I worked through the [ThinkStats](http://greenteapress.com/thinkstats/) curriculum that included an introduction to Pandas.

* I worked through [Jake Vanderplas' Data Science Handbook](https://github.com/jakevdp/PythonDataScienceHandbook) Jupyter Notebook modules.

* I listened to Michael Kennedy's [Talk Python To Me Podcasts](https://talkpython.fm/) on the way home from work each day.

That being said, not all of the first week's curriculum came easily. In particular, while I was already comfortable using Matplotlib and NumPy, Pandas was much harder to gain an intuitive sense for beyond just the basic operations.  I still have a long way to go in getting comfortable with using Pandas cleverly instead of just making for loops and dictionaries by hand. Also, I still suck at Git.

On a positive note, though, I think that my group's first project went well. Here were the details...

## First Assignment: Project Benson

The premise of Project Benson was to analyze the NYC Metropolitan Transportation Authority (MTA) [turnstile data](http://web.mta.info/developers/turnstile.html) in order to provide recommendations to a womens' tech organization as to which subway stations they should target in order to advertise for their annual gala.

The MTA data is pretty impressive - you can view the number of riders entering and exiting each station (even down to each turnstile!) within every four hour window. That is, after you've cleaned all of the data, of course, and gotten it in a form that you can work with.

In my group, we broke up work according to what time of day we were interested in stationing advertisers. Since I was assigned weekday morning commutes, I found the top 10 busiest station exits between 8 am and 12 pm (since the data was given in four hour intervals). The busiest stations were:

1. 34 ST-HERALD SQ
2. 47-50 STS ROCK
3. GRD CENTRL-42 ST
4. TIMES SQ-42 ST
5. 34 ST-PENN STATION
6. 23 ST
7. LEXINGTON AVE/53
8. 14 ST-UNION SQ
9. CANAL ST
10. FULTON ST

I was interested in placing advertisers in stations that were business districts, rather than touristy areas, in order to maximize the likelihood of reaching local tech workers. To that end, I examined which destination stations had the largest difference between weekday and weekend morning activity. I surmised that these stations were likely business districts. This was a good guess, as immediately, the tourist heavy stations of Penn Station, Canal Street, and 14th-Union Station were no longer in the top 10, and were replaced with Wall St, Chambers St, and Bryant Park in business districts.  The top ten stations with greatest difference between weekday and weekend morning volume were:

1. 47-50 STS ROCK
2. GRD CENTRL-42 ST
3. 34 ST-HERALD SQ
4. LEXINGTON AV/53
5. TIMES SQ-42 ST
6. 23 ST
7. 42 ST-BRYANT PK
8. WALL ST
9. FULTON ST
10. CHAMBERS ST

Not surprisingly, though, three of the top ten highest volume destinations on weekday mornings were in the financial district, which is not where many tech companies are located. I used the Google Maps API to superimpose the largest tech employers depicted with black circles (according to [Built in NYC](http://www.builtinnyc.com/2016/10/28/nyc-top-100-list)) with these ten station recommendations, and found that there were no subway recommendations currently near Twitter, Google, or Facebook. Thus, I suggested moving two of these financial district stations to be close to the Twitter/Google and Facebook locations (circled) and my final top ten recommendations as to where to station advertisers on weekday mornings were:

1. 47-50 STS ROCK
2. GRD CENTRAL-42 ST
3. 34 ST-HERALD SQ
4. 23 ST
5. ASTOR PLACE
6. 18TH ST
7. LEXINGTON AVE/53
8. TIMES SQ-42 ST
9. 42 ST-BRYANT PK
10. WALL ST
   
In summary, this project provided a great opportunity to get my hands on a real data set and be forced to analyze it well enough in order to provide valuable recommendations under a tight deadline. I can't wait for Week 2!

