## Objective Function

\text.block{definition}{objectiveFunction}{An *objective function* $\objf:\solutionSpace\mapsto\realNumbers$ rates the quality of a candidate solution $\solspel\in\solutionSpace$ from the solution space $\solutionSpace$ as real number.}

\text.block{definition}{objectiveValue}{An *objective value* $\objf(\solspel)$ of the candidate solution $\solspel\in\solutionSpace$ is the value that the objective function $\objf$ takes on for $\solspel$.}

Without loss of generality, we assume that all objective functions are subject to *minimization*, meaning that smaller objective values are better.
In this case, a candidate solution $\solspel_1\in\solutionSpace$ is better than another candidate solution $\solspel_2\in\solutionSpace$ if and only if $\objf(\solspel_1)<\objf(\solspel_2)$.
If $\objf(\solspel_1)>\objf(\solspel_2)$, then $\solspel_2$ would be better and for $\objf(\solspel_1)=\objf(\solspel_2)$, there would be no benefit of either solution over the other from the perspective of the optimization criterion $\objf$.
The minimization scenario fits to situations where $\objf$ represents a cost, a time requirement, or, in general, any number of required resources.

Maximization problems, i.e., there the candidate solution with the higher objective value is better, are problems where the objective function represents profits, gains, or any other form of positive output or result of a scenario.
Notice that maximization and minimization problems can be converted to each other by simply negating the objective function.
In other words, if $\objf$ is the objective function of a minimization problem, we can solve the maximization problem with $-\objf$ and get the same result.

From the perspective of a programmer, an objective function implements the interface given in [@lst:IObjectiveFunction].

\repo.listing{lst:IObjectiveFunction}{A general interface for objective functions.}{java}{src/main/java/aitoa/structure/IObjectiveFunction.java}{}{}