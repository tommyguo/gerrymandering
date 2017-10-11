# Gerrymandering
## What is it?
Every state is allotted a number of representatives, congressmen and congresswomen, based on
population. These elected officials represent the state in the House of Representatives. Each state
is split up into congressional districts, with one representative coming from each congressional
district.

In order to maintain fairness, congressional districts contain roughly the same number
of people. Thus, the ratio of people to representative is the same regardless of which congressional
district you are in, meaning you have as equal of a say as anyone else. It's important to note that
this equality only happens within a state. Some states have as high (or low, depending on how you
look at it) as 900,000 people/representative while other states have as low as around
500,000 people/representative.

The fact that congressional districts are bound to population makes them special. Other geographic
divisions that you're familiar with such as states, counties, boroughs, districts (not the same
as a congressional district), cities, and towns are independent of population. This means that
their boundaries almost never change.

In 1960, Philadelphia had a population of 2 million. Now, it's roughly 1.5 million. But its borders
have remained the same. Like Philadelphia, congressional districts also experience changes in
population. Unlike Philadelphia, congressional district borders must be redrawn so that all of them
have roughly the same population. This happens every 10 years after Census data comes in.

With few rules governing how congressional districts can be drawn, this can be easily abused to
favor one party, for example, by concentrating all minority votes into one congressional district.

## Why can't we stop this?
In our current system, the redrawing of congressional districts is inherently arbitrary. Since there
is no algorithm for redistricting, there will always be a certain human element involved, which
means that hard to prove biases can come into play. Furthermore, the party in control has a clear
incentive for gerrymandering. A great incentive + ease in obtaining the incentive = gerrymandering.

## Can we devise an algorithm to redraw congressional districts?
Devising an algorithm to redraw congressional district lines is by no means a perfect or even good
solution to the problem of gerrymandering. But it would be useful to see if it's a possibility.

On a high level, our algorithm's job seems easy:
1. Gather all the possible ways there are to split a state into congressional districts.
2. Choose the best one.

## Step 1
So how do we go about finding all possible ways to redistrict? Well, in the absolutely worse case we can
think of splitting a state into congressional districts as assigning each discrete geographic region
to a congressional district.

Note:
We have to work with discrete geographic regions or else there would be an infinite number
of ways to partition a state. But what geographic divisions should we use? Smaller geographic
regions like census tracts generally provide more granularity and more possible partitions. More
partitions means more options to choose from and generally a better "best" way to redistrict. However,
the tradeoff is that it becomes more computationally expensive.

This essentially boils down to the problem of how many ways to "partition a set of n objects into k non-empty subsets,"
which we can calculate using a [Stirling number of the second kind](https://en.wikipedia.org/wiki/Stirling_numbers_of_the_second_kind#Generating_function).

If we were to redistrict Pennsylvania with its 3,218 census tracts and 18 congressional districts,
we could see how many possibilities we would have to consider by plugging in 3,218 as n and 18
as k into the explicit formula for calculating the Stirling number of the second kind. This
number turns out to be [huge](https://www.wolframalpha.com/input/?i=1%2F18!+*+%E2%88%91+from+j+%3D+0+to+18+of+(-1)%5E(18-j)+*+(18+choose+j)+*+j%5E3218),
about 4.58 * 10<sup>180</sup>.

A supercomputer would take over 10<sup>146</sup> times the current age of the universe, according
to Wolfram Alpha, to do this. That's not exactly the timescale that we're looking for: ideally,
our algorithm should be able to take days to complete.

So how do we make our algorithm better? Well, instead of considering all partitions of a state, we
could only look at those that are valid *while* the algorithm is running. What we previously did
was find all partitions and then weed out those that don't meet base requirements such as equal
population, following political boundaries, and being compact. But what if we decide that our
partition of congressional district 1 is invalid. Then there's no reason to figure out all the ways
there are to partition the other 17 congressional districts. We can just stop the algorithm and move
onto finding another valid partition of congressional district 1.

This would greatly improve our runtime. But it's hard to say by how much. It really depends on the ratio
of invalid to valid partitions.

## Step 2
If we magically figure out Step 1, then Step 2 seems a lot easier. We just go through all possible
and valid partitions and figure it out which one is the best according to our metrics.

## Will this actually reduce bias and gerrymandering?
I definitely think an algorithm could reduce gerrymandering and make the process more transparent.
Politicians would only interact with the algorithm in two ways: determining the geographic
region to be used and what metric the "best" way to redistrict should be based on. While both
of these choices could be tinkered to favor one political party, it's a whole lot more transparent
and harder to manipulate. If politicians decide that the algorithm should favor less racial
diversity, then it's easy to see their intentions.
