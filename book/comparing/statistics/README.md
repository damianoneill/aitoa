## Statistical Measures

Most of the optimization algorithms that we have discussed so far are randomized ([@sec:randomizedAlgos]).
A randomized algorithm makes at least one random decision which is not a priori known or fixed.
Such an algorithm can behave differently every time it is executed.

\text.block{definition}{run}{One independent application of one optimization algorithm to one instance of an optimization problem is called a *run*.}

Each *run* is considered as independent and may thus lead to a different result.
This also means that the measurements of the basic performance indicators discussed in [@sec:basicPerformanceIndicators] can take on different values as well.
We may measure $k$&nbsp;different result solution qualities at the end of $k$&nbsp;times applications of the same algorithm to the same problem instance (which was also visible in&nbsp;[@fig:performance_indicators_cuts]).
In order to get a handy overview about what is going on, we often want to reduce this potentially large amount of information to a few, meaningful and easy-to-interpret values.
These values are statistical measures.
Of course, this here is neither a book about statistics nor probability, so we can only scratch on the surface of these topics.
For better discussions, please refer to text books such as&nbsp;[@K2014PTACC; @T2017LOPTAMS; @R1977PTACC; @T2018SFAB].

### Statistical Sample vs. Probability Distribution

One issues we need to clarify first is that there is a difference between a probability distribution and data sample.

\text.block{definition}{probabilityDistribution}{A *[probability distribution](http://en.wikipedia.org/wiki/Probability_distribution)*&nbsp;$F$ is an assignment of probabilities of occurrence to different possible outcomes in an experiment.}

\text.block{definition}{sample}{A [random sample](http://en.wikipedia.org/wiki/Sample_(statistics)) of length&nbsp;$k\geq 1$ is a set of $k$&nbsp;independent observations of an experiment following a random distribution&nbsp;$F$.}

\text.block{definition}{observation}{An *observation* is a measured outcome of an experiment or random variable.}

The specification of an optimization algorithm together with its input data, i.e., the problem instance to which it is applied, defines a probability distribution over the possible values a basic performance indicator takes on.
If I would possess sufficient mathematical wisdom, I could develop a mathematical formula for the probability of every possible makespan that the 1-swap hill climber `hc_1swap` without restarts could produce on the `swv15` JSSP instance within 100'000&nbsp;FEs.
I could say something like: "With 4% probability, we will find a Gantt chart with a makespan of 2885 time units within 100'000&nbsp;FEs (by applying `hc_1swap` to `swv15."
With sufficient mathematical skills, I could define such probability distributions for all algorithms.
Then, I would know absolutely which algorithm will be the best for which problem.

However, I do not possess such skill and, so far, nobody seems to possess.
Despite significant advances in modeling and deriving statistical properties of algorithms for various optimization problems, we are not yet at a point where we can get deep and complete information for most of the relevant problems and algorithms.

We cannot obtain the actual probability distributions describing the results.
We can, however, try to *estimate* their parameters by running experiments and measuring results, i.e., by sampling the results. 

|\#&nbsp;throws|number|$f_1$|$f_2$|$f_3$|$f_4$|$f_5$|$f_6$|
|--:|:-:|--:|--:|--:|--:|--:|--:|
|1|5|0.0000|0.0000|0.0000|0.0000|1.0000|0.0000|
|2|4|0.0000|0.0000|0.0000|0.5000|0.5000|0.0000|
|3|1|0.3333|0.0000|0.0000|0.3333|0.3333|0.0000|
|4|4|0.2500|0.0000|0.0000|0.5000|0.2500|0.0000|
|5|3|0.2000|0.0000|0.2000|0.4000|0.2000|0.0000|
|6|3|0.1667|0.0000|0.3333|0.3333|0.1667|0.0000|
|7|2|0.1429|0.1429|0.2857|0.2857|0.1429|0.0000|
|8|1|0.2500|0.1250|0.2500|0.2500|0.1250|0.0000|
|9|4|0.2222|0.1111|0.2222|0.3333|0.1111|0.0000|
|10|2|0.2000|0.2000|0.2000|0.3000|0.1000|0.0000|
|11|6|0.1818|0.1818|0.1818|0.2727|0.0909|0.0909|
|12|3|0.1667|0.1667|0.2500|0.2500|0.0833|0.0833|
|100|&hellip;|0.1900|0.2100|0.1500|0.1600|0.1200|0.1700|
|1'000|&hellip;|0.1700|0.1670|0.1620|0.1670|0.1570|0.1770|
|10'000|&hellip;|0.1682|0.1699|0.1680|0.1661|0.1655|0.1623|
|100'000|&hellip;|0.1671|0.1649|0.1664|0.1676|0.1668|0.1672|
|1'000'000|&hellip;|0.1673|0.1663|0.1662|0.1673|0.1666|0.1664|
|10'000'000|&hellip;|0.1667|0.1667|0.1666|0.1668|0.1667|0.1665|
|100'000'000|&hellip;|0.1667|0.1666|0.1666|0.1667|0.1667|0.1667|
|1'000'000'000|&hellip;|0.1667|0.1667|0.1667|0.1667|0.1667|0.1667|

: The results of one possible outcome of an experiment with several simulated dice throws. The number&nbsp;*\# throws* and the thrown *number* are given in the first two columns, whereas the relative frequency of occurrence of number&nbsp;$i$ is given in the columns&nbsp;$f_i$. {#tbl:diceThrow}

Think about throwing an ideal dice.
Each number from one to six has the same probability to occur, i.e., the probability $\frac{1}{6}=0.166\dots6$.
If we throw a dice a single time, we will get one number.
If we throw it twice, we see two numbers.
Let&nbsp;$f_i$ be the [relative frequency](http://en.wikipedia.org/wiki/Frequency_(statistics)) of each number in $k=\textnormal{\# throws}$ of the dice, i.e., $f_i=\frac{\textnormal{number of times we got }i}{k}$.
The more often we throw the dice, the more similar should&nbsp;$f_i$ get to&nbsp;$\frac{1}{6}$, as illustrated in [@tbl:diceThrow] for a simulated experiments with of many dice throws.

As can be seen in [@tbl:diceThrow], the first ten or so dice throws tell us very little about the actual probability of each result.
However, when we throw the dice many times, the observed relative frequencies become more similar to what we expect.
This is called the [Law of Large Numbers](http://en.wikipedia.org/wiki/Law_of_large_numbers) &ndash; and it holds for the application of optimization algorithms too.

The takeaway message is: It is *never* enough to just apply an optimization algorithm once or twice to a problem instance to get a good impression of the expected solution quality.
It is a good rule of thumb to always perform at least 20 independent runs.
In our experiments on the JSSP, we did 101 runs per problem instance. 

### Arithmetic Mean vs. Median

Assume that we have obtained a sample&nbsp;$A=(\arrayIndex{a}{0},\arrayIndex{a}{1}, \dots, \arrayIndex{a}{n-1}$ of $n$&nbsp;observations of an experiment, e.g., we have measured the quality of the best discovered solutions of 101 independent runs of an optimization algorithm.
We usually want to get reduce this set of numbers to a single value which can give us an impression of what the "expected outcome" (or result quality is).
Two of the most common options for doing so is to either compute the *arithmetic mean* or the *median*.

\text.block{definition}{arithmeticMean}{The [arithmetic mean](http://en.wikipedia.org/wiki/Arithmetic_mean) $\mean(A)$ is an estimate of the expected value of a data sample $A=(\arrayIndex{a}{0},\arrayIndex{a}{1}, \dots, \arrayIndex{a}{n-1}$. It is computed as the sum of all&nbsp;$n$ elements&nbsp;$\arrayIndex{a}{i}$ in the sample data&nbsp;$A$ divided by the total number&nbsp;$n$ of values.
$$ \mean(A) = \frac{1}{n} \sum_{i=0}^{n-1} \arrayIndex{a}{i} $$
}
 
\text.block{definition}{median}{The median $\median(A)$ is the value separating the bigger half from the lower half of a data sample or distribution. It is the value right in the middle of a *sorted* data sample $A=(\arrayIndex{a}{0},\arrayIndex{a}{1}, \dots, \arrayIndex{a}{n-1}$ where $\arrayIndex{a}{i-1}\leq\arrayIndex{a}{i} \forall 0<i<n$.

$$ \median{A} = \left\{\begin{array}{ll}
\arrayIndex{a}{\frac{n}{2}} & \textnormal{if }n\textnormal{ is odd}\\
\frac{1}{2}\left(\arrayIndex{a}{\frac{n}{2}-1} + \arrayIndex{a}{\frac{n}{2}}\right) & \textnormal{otherwise}%
\end{array}\right. \quad \textnormal{iff }\arrayIndex{a}{i-1}\leq\arrayIndex{a}{i} \forall 0<i<n $$
}