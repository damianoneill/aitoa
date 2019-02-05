## Hill Climbing

Our first algorithm, random sampling, is not a very efficient.
It does not make any use of the information it "sees" during the optimization process.
A search step consists of creating an entirel new, entirely random candidate solution.
Every search step is thus independent of all prior steps.

Local search algorithms offer an alternative.
They remember the current best point&nbsp;$\bestSoFar{\sespel}$ in the search space&nbsp;$\searchSpace$.
In every step, a local search algorithm investigates a point&nbsp;$\sespel$ similar to&nbsp;$\bestSoFar{\sespel}$.
If it is better, it is accepted as the new best-so-far solution.
Otherwise, it is discarted.

\text.block{definition}{causality}{Causality means that small changes in the features of an object (or candidate solution) also lead to small changes in its behavior (or objective value).}

Local search exploits a property of many optimization problems called *causality*&nbsp;[@R1973ES; @R1994ES; @WCT2012EOPABT; @WZCN2009WIOD].
The idea is that if we have a good candidate solution, then there may exist similar solutions which are better.
We hope to find one of them and then continue trying to do the same from there.

### Ingredient: Unary Search Operation for the JSSP

So the question arises how we can create a candidate solution which is similar to &nbsp; but also slightly different from one &nbsp; we already have?
Our search algorithms are working in the search space&nbsp;$\searchSpace$.
So we need one operation which accepts an existing point&nbsp;$\sespel\in\searchSpace$ and produces a slightly modified copy of it as result.
In other words, we need to implement a unary search operator!

On a JSSP with $\jsspMachines$&nbsp;machines and $\jsspJobs$&nbsp;jobs, our representation&nbsp;$\searchSpace$ encodes a schedule as an integer arry of length&nbsp;$\jsspMachines*\jsspJobs$ containing each of the job IDs (from $0\dots(\jsspJobs-1)$) exactly&nbsp;$\jsspMachine$ times.
The sequence in which these job IDs occur then defines the order in which the jobs are assigned to the machines, which is realized by the representation mapping&nbsp;$\repMap$ (see [@lst:JSSPRepresentationMapping]).

One idea to create a slightly modified copy of such a point&nbsp;$\sespel$ in the search space would be to simply swap two of the jobs in it.
Such a&nbsp;`1swap` operator can be implemented as follows:

1. Make a copy&nbsp;$\sespel'$ of&nbsp;$\sespel$
2. Pick a random index&nbsp;$i$ from $0\dots(\jsspMachines*\jsspJobs-1)$.
3. Pick a random index&nbsp;$j$ from $0\dots(\jsspMachines*\jsspJobs-1)$.
4. If the values at indexes&nbsp;$i$ and&nbsp;$j$ in&nbsp;$\sespel'$ are the same, then go back to 3. (Swapping the same values makes no sense, since then the value of&nbsp;$\sespel'$ and&nbsp;$\sespel$ would be the same at the end, so also their mappings&nbsp;$\repMap(\sespel)$ and&nbsp;$\repMap(\sespel')$ would be the same, i.e., we would actually not make a "move".)
5. Swap the values at indexes&nbsp;$i$ and&nbsp;$j$ in&nbsp;$\sespel'$.

We implement this operator in [@lst:JSSPUnaryOperator1Swap].
Notice that the operator is randomized, i.e., appling it twice to the same point in the search space will likely yield different results.

\repo.listing{lst:JSSPUnaryOperator1Swap}{An excerpt of the `1swap` operator for the JSSP, an implementation of the unary search operation interface [@lst:IUnarySearchOperator]. `1swap` swaps two jobs in our encoding of Gantt diagrams.}{java}{src/main/java/aitoa/examples/jssp/JSSPUnaryOperator1Swap.java}{}{relevant}

In [@fig:jssp_unary_1swap_demo], we illustrate the application of this operator to one point&nbsp;$\sespel$ in the search space for our&nbsp;`demo` JSSP instance.
It swaps the two jobs at index&nbsp;$i=10$ and&nbsp;$j=15$ of&nbsp;$\sespel$.
In the new, modified copy&nbsp;$\sespel'$, the jobs&nbsp;$3$ and&nbsp;$0$ at these indices have thus traded places.
The impact of this modification becomes visible when we map both&nbsp;$\sespel$ and&nbsp;$\sespel'$ to the solution space using the representation mapping&nbsp;$\repMap$.
The&nbsp;$3$ which has been moved forward now means that job&nbsp;$3$ will be scheduled before job&nbsp;$1$ on machine&nbsp;$2$.
As a result, the last two sub-jobs of job&nbsp;$3$ can now finish earlier on machines&nbsp;$0$ and&nbsp;$1$, respectively.
However, time is wasted on machine&nbsp;$2$, as we first need to wait for the first two sub-jobs of job&nbsp;$3$ to finish before we can execute it there.
Also, job&nbsp;$1$ finishes now later on that machine, which also delays its last sub-job to be executed on machine&nbsp;$4$.
This pushes back the last sub-job of job&nbsp;$0$ (on machine&nbsp;$4$) as well.
The new candidate solution&nbsp;$\repMap(\sespel')$ thus has a longer makespan of&nbsp;$\objf(\repMap(\sespel'))=195$ compared to the original solution with&nbsp;$\objf(\repMap(\sespel'))=180$.

![An example for the application of `1swap` to an existing point in the search space (top-left) for the `demo` JSSP instance. It yields a slightly modified copy (top-right) with two jobs swapped. If we map these to the solution space (bottom) using the representation mapping&nbsp;$\repMap$, the changes marked with violet frames occur (bottom-right).](\relative.path{jssp_unary_1swap_demo.svgz}){#fig:jssp_unary_1swap_demo width=99%}

In other words, our application of&nbsp;`1swap` in [@fig:jssp_unary_1swap_demo] has led us to a worse solution.
This will happen most of the time.
As soon as we have a good solution, the solutions similar to it tend to be worse.
However, if we would have been at&nbsp;$\sespel'$ instead, an application of `1swap` could well have resulted in&nbsp;$\sespel$.
Often, the chance to find a really good solution by iteratively sampling the neighborhoods of good solutions is higher than trying to randomly guessing them (as `rs` does) &nbsp; even if most of our samples are worse.