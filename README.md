Lab \#1
================

### in class Thursday Sept 5, 2024

### Econ B2000, MA Econometrics

### Kevin R Foster, CCNY
<img src="lab1.jpg" style="width:15.0%" />

Overall Goal: you will be assessing the fairness of the dice of the
people in your group. Each student had a chance to “adjust” the dice, to
see if they roll a 6 more or less often than would be expected. *(Or
roll some other number than 6 preferentially – some of you have
different dice. In this I’ll say “6” but you can substitute your own
chosen number.)* But how would we really know - what is “more often”?
Obviously some dice will look grossly misshapen but for now we’ll
concentrate on just the results: did a 6 come up, or not?

Create some Experiment Protocols **before** looking at the data from the
dice rolls. Plan how to test and state what conclusions would be drawn,
from certain outcomes.

**Possible Protocol 1 (PP1)**: roll once; if get 6 then conclude the
dice is not fair; if roll any other number then conclude it is fair.
Analyze PP1: if the dice were fair, what is the probability it would be
judged to be unfair? Oppositely, if the dice were unfair, what is the
probability that it would be judged to be fair?

**PP2**: roll the dice 20 times. (Each person should have done this
beforehand.) Group can specify a decision rule to judge that dice is
fair or unfair. Consider the stats question: if fair dice are rolled 20
times, what is likely number of 6 resulting? How unusual is it, to get 1
more or less than that? How unusual is it, to get 2 more or less? 3?
Analyze PP2 including the question: if the dice were fair, what is the
chance it could be judged as unfair?

**PP3**: roll 100 times and specify decision rules. Some cases are easy:
if every single roll comes to 6 then might quickly conclude. But what
about the edge cases? Is it fair to say that every conclusion has some
level of confidence attached? Where do you set boundaries for decisions?
Analyze PP3. What is the chance that fair dice could be judged to be
unfair?

Now devise your own **experiment protocol** (not ‘possible’ anymore but
actual so call it **EP1**). Analyze it before looking at the data. What
is a reasonable number of times to roll your experiment dice? (given
time available in class, whether group members did the homework and
brought info on 20 rolls, etc) If you roll the experiment dice a certain
number of times and see a particular outcome, then you would conclude it
is fair or not. How confident would you be, in any of those conclusions?
Note that while previous Possible Protocols emphasized counting just the
number of times a 6 came up, you might consider other outcomes such as
the average value of the dice when rolled or the distribution of other
outcomes (what number is on opposite face from 6? Do you suppose that
number might be disproportionately represented if dice were not fair?).

OK *now you can actually look at the data*, maybe actually roll the
dice. Only now will you actually perform the experiment. What conclusion
do you draw from your experiment? How confident are you, in this
conclusion? How would you revise your protocol if you were to devise
**EP2**?

Your group’s short presentation should explain justification for your
EP1, your experiment results, conclusion and confidence in these
results, and what modifications you would make for EP2.

For homework, should explain your choices of PP3 and analysis. Then you
are free to set out run EPn, for higher values of n.

### Some background on simulations with R

You can have the computer roll dice. The command, `sample()`, will do it
nicely. This command below takes 20 draws from the integers 1 to 6. The
metaphor is not quite rolling dice, but as if there were 6 balls in a
jar, labeled with a number, and it picks a ball from the jar 20 times.
So that is why the `replace=TRUE` command is important, otherwise after
it takes out a ball it doesn’t put it back so couldn’t run that more
than 6 times.

``` r
how_many_rolls <- 20
sim_rolls <- sample(1:6, how_many_rolls, replace = TRUE)
```

Note that I’ve made a variable, `how_many_rolls`, so that it’s easy to
go back and change that once and have that change propagate through the
rest of the program. It’s a useful habit to get into.

That gives one version of EP2, so that is one example of possible
outcomes. But if we want to get a better sense of the range of outcomes
to be able to say what is typical or expected, we would want to do that
a lot of times.

There are a variety of ways to do that. One possible way is a loop.
Loops are not efficient (they can really slow down your program) but
might be easier to understand. I’ll show other ways too.

``` r
# for first time
lots_of_sim_rolls <- sample(1:6,how_many_rolls, replace = TRUE)

# do 49 more simulations
for (indx in 1:49) {
  sim_rolls <- sample(1:6,how_many_rolls, replace = TRUE)
  lots_of_sim_rolls <- data.frame(lots_of_sim_rolls,sim_rolls)
  }
```

This creates a data frame of 50 variables (one for each simulation)
where each simulation has 20 rolls in it. It’s like a matrix with 20
rows and 50 columns.

If we didn’t want to do a loop (narrator voice: we usually don’t want to
do loops) we could instead create one big long vector, with one column
but many more rows. We just keep track that the first 20 observations
are for the first simulation, the next 20 are for the next simulation,
and so forth – we end up with 20x50 = 1000 observations in this single
vector.

``` r
how_many_sims <- 50
sim_rolls_vec <- sample(1:6,(how_many_rolls*how_many_sims), replace = TRUE) # vectorized version
```

Now if we want to analyze these rolls, for instance to see how many come
up as 6, we can do that easily.

``` r
if_come_up_6 <- as.numeric(lots_of_sim_rolls == 6)
mean(if_come_up_6)
```

    ## [1] 0.186

``` r
if_come_up_6_vec <- as.numeric(sim_rolls_vec == 6)
mean(if_come_up_6_vec)
```

    ## [1] 0.171

A few notes. You could write

``` r
if_come_up_6 <- (lots_of_sim_rolls == 6)
```

without the `as.numeric`, which would generate a list of logical values
either True or False. But I know we want to then take an average, where
the True is set to be 1 and False is 0, so I put those steps together.
(Think a bit to satisfy that if 3 out of 20 evaluate as True then 3/20
is the average value.)

Also note that, for complicated historical reasons, we don’t want to use
just “=” but rather “==”. If you were to write just a single equals
sign, that would tell it to *assign* the value of 6 to every component
of `lots_of_sim_rolls`. Which is not what we want. But a double equals
sign is asking the question so outputs True/False depending on whether
the equality is true or not. For this case, you could get the same
result with `'(lots_of_sim_rolls > 5)` or `(lots_of_sim_rolls >= 6)` or
even `!(lots_of_sim_rolls < 6)` because ! signifies logical NOT.

Note that for this case it doesn’t matter whether I take the average of
each simulation and then the average of those averages, versus taking
one giant average. So for the case of the matrix of results, I could
worry about taking the average of each column, then taking the average
of those averages. But that’s the same as taking the average of all 1000
together since the columns are each the same length.

Note that the average values of these 2 versions, matrix or vector, are
going to be different, just due to the inherent randomness. As you
increase how_many_sims, they should get closer – that’s Law of Large
Numbers. You can experiment to see.

Note that I’ve split the code into first creating simulated values from
1 to 6, then a separate step to check if those values are equal to 6.
That way if I had a different PP, perhaps if I wanted to look at the
average value of the numbers rolled, I can change just that second step.
Or if you want to check how many rolls came out to have a value of 1, or
whatever other PP you might devise. But this takes extra memory storage
so sometimes, maybe if you had a million simulations of 500,000 rolls,
you might want to compact down.
