+++
title = "visualizing m-lab data with bigquery: part two"
author = "dominic"
date = "2012-12-10"
categories = [
  "python"
]
tags = [
  "python",
  "dataviz",
  "m-lab",
]
+++

# Visualizing M-Lab data with BigQuery: Part Two

When I wrote [Part
One](/blog/visualizing-m-lab-data-with-bigquery/ "Visualizing M-Lab data with BigQuery"),
I wasn’t aware that it was going to need a Part Two. However, I was unsatisfied
with the JavaScript version and the limitations incurred by trying to build a
live app. I also wanted to make movies to show the time series of the data. As
such, I rewrote the code in Python using the authorization technique for
installed apps, and you can find that
[here](http://analysis.m-lab.googlecode.com/git/geoviz "geoviz @ m-lab.analysis").

I won’t go through the code in depth, because it’s fairly explanatory.  It
authorizes with, and gets, a BigQuery service then loops through the tables in
the m\_lab datastore running a query and plotting points on a map. However, the
results of running it are much more satisfactory.

{{<video src="./minrtt_map.mp4" controls="no">}}

If you have any questions, feel free to comment here or contact me some other
way.

