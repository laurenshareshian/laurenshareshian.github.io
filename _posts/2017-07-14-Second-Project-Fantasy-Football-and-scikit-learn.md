---
layout: post
Title: Regression Project: Predicting Fantasy Football Leaders
---

Our second project at Metis involved using scikit-learn and regression models in order to analyze a data set of our choosing. I chose to analyze fantasy football performance, of course, as I wanted to improve my chances of slaughtering my fantasy opponents this season. Each pre-season, ESPN puts out projected rankings of how each player will perform. In September of 2016, released the [top 300 rankings](http://www.espn.com/fantasy/football/story/_/id/16287927/2016-fantasy-football-rankings-top-300). You can view the top 10 ESPN ranked players and the top 10 actual top fantasy leaders for the 2016 season below:

![espn](/images/espn.png “ESPN projected leaders”) ![actualleaders](images/actualleaders.png “actual leaders”)

As we can see, ESPN erroneously predicted Adrian Peterson to be the third highest fantasy scorer, and totally underestimated how well quarterbacks like Aaron Rodgers and Matt Ryan would do. Who were the most under and overestimated players using ESPN’s rankings? Let’s view those below by sorting the residuals:

![uner](/images/under.png “Understimated Players”) ![over](images/over.png “Overestimated Players”)
