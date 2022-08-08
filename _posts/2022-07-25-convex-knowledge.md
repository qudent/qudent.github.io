---
title: 'The \mathbb{R}-algebra of quasiknowledge and convex optimization'
date: 2022-07-25
permalink: /posts/2022/07/convex-knowledge/
tags:
  - cs.ml
  - cs.cc
  - quant-ph
---
(major rewrite+updates ongoing, old version [here](https://github.com/qudent/qudent.github.io/blob/935968fec7d4e89e7953f460d1c2b1093bf0da6b/_posts/2022-07-25-convex-knowledge.md). The old version is more verbose/philosophical, in this one, I try to avoid burying the lede and put the definitions first.)
## 1. Abstract
I develop a convex description of a classical or quantum learner's or agent's state of knowledge and environmental state, which would allow using convex optimization and duality on problems related to optimal learning or actions if one restricted to finite-dimensional vector spaces. The physically possible states are described as a convex subset of an associative, commutative $\mathbb{R}$-algebra. This also allows solving formal differential equations and power series that describe e.g. the evolution of a learner's knowledge over time when it observes a Poisson process generating experimental data.

The outline: In section 2-6, I develop the concrete situations of classical, pure quantum, and mixed quantum physics and obtain structures $\mathcal{S}^\pm\_{\mathrm{class}},$ $\mathcal{S}^\pm\_{\mathrm{quant}},$ $\mathcal{S}^\pm\_{\mathrm{decoh}}.$ More precisely, section 2 defines sets, sections 3-5 structure on that sets, and section 6 an equivalence relation that makes equivalent "states of quasiknowledge" equal. Then section 6 mentions the algebraic axioms that these structures fulfill. The remaining sections are devoted to applications.

**Remark:** If you want to speed up reading or don't know quantum physics, you can skip all mentions of non-classical situations.

## 2. Sets before modding out the equivalence relation
Consider an agent within an environment described by $e\in E,$ with some internal memory state $m\in M.$
1. In the classical case, we describe the situation by a probability matrix $(p_{e,m})\_{(e,m)\in E\times M}\in(\mathbb{R}^+)^{E\times M},$ where $\mathbb{R}^+$ denotes the set of **nonnegative** real numbers,
2. in the quantum case without decoherence, we have a bipartite quantum state $(\psi_{e,m})\_{(e,m)\in E\times M}\in\mathbb{C}^{E\times M},$ and
3. we model the quantum case with decoherence by adding an additional subsystem $D$ into which the state is supposed to have decohered, and and consider a pure quantum state $(\psi\_{d,e,m})\_{(d,e,m)\in D\times E\times M}\in\mathbb{C}^{D\times E\times M}$ again.[^1]

For a fixed $E,$ we denote and define sets of possible situations (which will turn into our desired notions of "states of knowledge" after modding out equivalence relation in section 5):
1. $\mathcal{S}'\_{\mathrm{class}}:=\left\\\{(M,P)\mid \left\|M\right\|<\infty, P\in(\mathbb{R}^+)^{E\times M}\right\\\},$
2. $\mathcal{S}'\_{\mathrm{quant}}:=\left\\\{(M,\Psi)\mid |M|<\infty, \Psi\in\mathbb{C}^{E\times M}\right\\\},$
3. $\mathcal{S}'\_{\mathrm{decoh}}:=\left\\\{(D,M,\Psi)\mid \left\|D\right\|<\infty, |M|<\infty, \Psi\in\mathbb{C}^{D\times E\times M}\right\\\}.$

In other words, we do not fix $M$ and $D$ (or limit their sizes), but consider them part of the description. Furthermore, we don't force the classical probability distributions or quantum states to be normalized. We write $\mathcal{S}'$ in developments that apply to any of $\left\\\{\mathcal{S}'\_{\mathrm{class}},\mathcal{S}'\_{\mathrm{quant}},\mathcal{S}'\_{\mathrm{decoh}}\right\\\}.$

We define $\mathcal{S}'^\pm:=\mathcal{S}'\times\mathcal{S}'$ (for classical, coherent-quantum, or decohering-quantum states). Denote a tuple $(S'\_1,S'\_2)\in\mathcal{S}'^\pm$ by $S'\_1-S'\_2$ and interpret it as a formal difference accordingly.

As discussed in the outline, we spend the next sections defining more structure on the sets before modding out the right equivalence relation in section 6.
## 3. Special elements
In $\mathcal{S}'\_\mathrm{class}$ and $\mathcal{S}'\_\mathrm{quant}$, for $\alpha\in\mathbb{R}^+$, we define $e\in E$ by $\mathbf{e}:=(\\\\{0\\\},(\delta\_{e',e})\_{(e,0)\in E\times M})$ In $\mathcal{S}'\_\mathrm{decoh},$ we use a one-dimensional decohering space and obtain $\mathbf{e}:=(\\\\{0\\\},\\\{0\\\},(\delta\_{e',e})\_{(0,e,0)\in D\times E\times M}).$

## 4. Set inclusions (injective maps)
We define maps that will play the role of inclusion maps after our equivalence relation (i.e. will be/stay injections):
1. $\mathcal{S}'\_\mathrm{class}\to\mathcal{S}'\_\mathrm{decoh},$ $(M,P)\to(E\times M, (\delta\_(e,m)\delta\_(e',m')\sqrt{P\_{e,m}})\_{(e,m),e',m'})$ (in words, treating a classical as a completely decohered state),
2. $\mathcal{S}'\_\mathrm{quant}\to\mathcal{S}'\_\mathrm{decoh},$ $(M,\Psi)\to(\\\\{0\\\\}, (\sqrt{P\_{e,m}})\_{0,e,m})$ (in words, treating a pure quantum state as a quantum state with no decoherence),
3. $\mathcal{S}'\to\mathcal{S}'^\pm,$ $S'\to S'-0$.

We also consider the nonnegative real numbers $\mathbb{R}^+$ as a subset of (the intersection of all) $\mathcal{S}'$, and all real numbers as a subset of (the intersection of all) $\mathcal{S}'^\pm$. Given $r\geq 0$, we choose a 1-dimensional memory (and, for $\mathcal{S}'_\mathrm{decoh}$, decohering) space, and let $P$ be an all-$r$ matrix for $\mathcal{S}'_\mathrm{class}$. In $\mathcal{S}'_\mathrm{quant}$ and $\mathcal{S}'_\mathrm{decoh}$, this corresponds to an all-$\sqrt{r}$ matrix. We embed real numbers $-r'<0$ in $\mathcal{S}'^\pm$ as $0-r'$, i.e. as the negation of the embedding of $r'$.

## 5. Operations
We now define some operations on the $\mathcal{S}'$ and $\mathcal{S}'^\pm.$
1. Addition, $+,$ is to be interpreted as the environment+agent being in either of the situations that the summands describe, and defined with direct sums:
   1. In $\mathcal{S}'\_{\mathrm{class}},$ $(M\_1,P\_1)+(M\_2,P\_2):=(M\_1\biguplus M\_2, P'),$ with $M\_1\biguplus M\_2$ denoting the disjoint union of $M\_1$ and $M\_2$ and the elements of $P'$ composed of the elements of $P\_1$ and $P\_2.$
   2. In $\mathcal{S}'\_{\mathrm{quant}},$ the definition is equivalent.
   3. In $\mathcal{S}'\_{\mathrm{decoh}},$ $(D\_1,M\_1,\Psi\_1) + (D\_2,M\_2,\Psi\_2) := (D\_1 \biguplus D\_2, M\_1 \biguplus M\_2, \Psi'),$ with the elements of $\Psi'\_{d,e,m}$ composed of those of $\Psi'\_1,\Psi'\_2$ if they $d$ and $m$ belong to the same summand and set to $0$ otherwise.

   In the $\mathcal{S}'^\pm,$ $(A-B) + (C-D) := ((A+C) - (B+D))$ as expected.
2. We denote multiplication on the $\mathcal{S}'$ by concatenating the factors and interpret it as the model having access to two pieces of knowledge independently. That is:
   1. In $\mathcal{S}'\_{\mathrm{class}},$ $(M\_1,P\_1)(M\_2,P\_2) := (M\_1 \times M\_2, P')$ with $P'\_{e, (m\_1, m\_2)} := (P\_1)\_{e, m\_1} (P\_2)\_{e, m\_2},$
   2. equivalently in $\mathcal{S}'\_{\mathrm{quant}},$
   3. and in $\mathcal{S}'\_{\mathrm{decoh}},$ $(D\_1,M\_1,\Psi\_1) (D\_2,M\_2,\Psi\_2) := (D\_1 \times D\_2, M\_1 \times M\_2, \Psi')$ with $\Psi'\_{(d\_1, d\_2), e, (m\_1, m\_2)}:= (\Psi\_1)\_{d\_1, e, m\_1}\Psi\_2\_{d\_2, e, m\_2}.$

   In the $\mathcal{S}'^\pm,$ $(A-B)(C-D) := (AC+BD) - (AD+BC).$
3. When we consider different possible environments, we denote our sets by $\mathcal{S}'^E$ and similar. Considering a bipartite environment $E\times C,$ i.e. a bipartite environment, we define a "partial trace" $\mathrm{tr}\_C \colon \mathcal{S}'^{E\times C}\to \mathcal{S}'^E.$ This works by considering the $C$ register, which previously was part of the environment, as part of the memory: it maps $M\to M\times C,$ copies the entries of $P$ or $\Psi$ and (in the decohering case) leaves $D$ untouched. After modding out an equivalence relation in section 6, this will look more like the partial trace  - after making some information accessible to the agent, the state of knowledge becomes equivalent with respect to all transformations that it can perform on that information.

   Of course, on the $\mathcal{S}'^\pm,$ the $\mathrm{tr}\_C (A-B):= \mathrm{tr}\_C A - \mathrm{tr}\_C B.$

   The partial trace will be useful to discuss the interface between the agent and the environment, i.e. discuss the possibility that the agent makes observations or performs actions that influence the next system state.
4. We define a preorder $S\_1 \leq S\_2$ to capture the notion that the agent can trivially transform a state into another state, as follows: [^4]
   1. In $\mathcal{S}'\_{\mathrm{class}},$ $(M\_1,P\_2 T) \leq (M\_2,P\_2)$ for all transformation matrices on the memory $T \in {(\mathbb{R}^+)}^{M\_1 \times M\_2}$ with $\Vert T\Vert\_1\leq 1.$ The latter is the [1-norm of $T,$ i.e. the maximal column sum.](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms) This means that starting from any state $m\_1\in M\_1,$ the probabilities of transitioning to a final state must sum to **at most** one; if it is $<1$ for some column, we interpret this as the agent giving up with a certain probability. If _all_ column sums were $1,$ we'd get a "proper" transition matrix that doesn't lose probability mass.
   2. The definition in $\mathcal{S}'\_{\mathrm{quant}}$ is analogous (with $\mathbb{R}^+$ replaced by $\mathbb{C}$) except that our constraint on $T$ is that $\Vert T\Vert\_2\leq 1,$ using the [2-norm of $T$](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms). This means that the maximal singular value of $T$ is $\leq 1.$ Analogously to 1., _all_ singular values being $1$ is equivalent to $T$ being an isometry, i.e. a "proper" quantum transition matrix. If some singular values of $T$ are $<1,$ the block-matrix $\begin{pmatrix}T\\\\ \sqrt{I-T^\dagger  T}\end{pmatrix}$ is an isometry nevertheless, so we can consider $T$ to be one [Kraus operator of a quantum channel](https://en.wikipedia.org/w/index.php?title=Quantum_operation&oldid=1094787035#Statement_of_the_theorem).
   3. In $\mathcal{S}'\_{\mathrm{quant}},$ we want to consider transitions between both $D$ and the memory. So our transformation is a tuple $(D\_M, T\_M, T\_D)$ with $T\_M \in \mathbb{C}^{M\_2 \times (M\_1 \times D')}$ and $T\_D\in \mathbb{C}^{(D\times D\_M)\times D\_1},$ and $\Vert T\_M\Vert\_2\leq 1,$ $\Vert T\_D\Vert\_2\leq 1.$ So $T\_M$ transforms the memory and generates a register $D\_M$ that becomes part of the decohering space, and $T\_E$ is then an arbitrary transformation of that decohering space, which is **not necessarily an isometry either**. 
   We denote the application of both as $T\_D(\Psi\_2 T\_M))$; in conclusion, our inequalities are $(D\_1,M\_1, T\_D(\Psi\_2 T\_M)) \leq (D\_2, M\_2, \Psi\_2)$. [^9]

   On $\mathbb{S}'^\pm,$ $(A-B)\leq (C-D)$ iff $A+D\leq B+D.$

## 6. The equivalence relation and the algebra
Finally, we define an **equivalence relation** on the $\mathcal{S}'$ and $\mathcal{S}'^\pm$ by $X\sim Y\leftrightarrow (X\leq Y \wedge Y\leq X)$, and call the equivalence classes the spaces of **states of knowledge** $\mathcal{S}$ and **states of quasiknowledge** $\mathcal{S}^\pm$.[^10]

It is tedious but straightforward to check (fingers crossed) that all our operations behave well with this equivalence relation and $\mathcal{S}\subseteq\mathcal{S}^\pm$ becomes a **convex subset of an associative, commutative ordered $\mathbb{R}$-algebra**.[^13]

The partial trace $\mathrm{tr}\_C\colon\mathcal{S}^{\pm (E\times C)}\to \mathcal{S}^{\pm E}$ is a **linear map** that surjectively maps states of knowledge to states of knowledge (i.e. the image $\mathrm{tr}\_C(\mathcal{S}^{E\times C})=\mathcal{S}^{E}$). Conversely, however, the preimage of a SOK is _not_ necessarily a SOK.


### 6.1. Properties of the partial trace
a linear map
1. linear,
2. The image of the states of knowledge (i.e. members of $\mathcal{S}^{E\times C}$) is $\mathrm{tr}\_C(\mathcal{S}^{E\times C})=\mathcal{S}^{E}$,

   TODO we still don't have the formal statement needed to bound the error in the uqa

## An agent interacting with its environment
Now consider an agent interacting with its environment. So we introduce some output and input subsystems $O,$ $I.$ Suppose we start with a state $S\in \mathcal{S}^{E}.$ In one step, the following happens:

1. The agent chooses to write something in $O,$ choosing the action it takes next. After that, we consider the result as part of the environment. So it can choose any $\left\{ S\mid ^{E\times I} \right\}$
2. The environment evolves under a transformation $T:\mathcal{S}^{E\times O}\to \mathcal{S}^{E\times I}.$ This transformation should be instantiated as a linear map acting on $E\times O,$ with the appropriate bounds on the $1$-norm/$2$-norm, as in our definition of the partial order.
3. and the agent reads in the input subsystem, making it part of its "state of knowledge".

After the first step, the agent can create any state $S\in\mathcal{S}^{E\times O}$ with $\mathrm{tr}\_O S=
## Operations
The basic plan is as follows:
1. We start with a naive description of states of knowledge [of an agent about its environment] as collections/matrices of conditional probabilities p(internal memory state|environmental ground truth)≥0; for now, we drop the requirement that the conditional probabilities sum to $1$ for any possible ground truth. We call these CPMs (conditional probability matrices); we consider all possible sets of internal memory states at once (i.e. don't fix the number of rows). The set of possible ground truths is fixed; we denote it by $D.$


## Introduction
Consider a learner in a (for now, classical) environment described by a ground truth $d\in D.$ The learner wants to find out something about the environment (e.g. output some function $f(d)$ with low error probability for any $d$). It can't influence $d$ (therefore I don't call it "agent"), but may have a choice of which "experiment" to perform in any step, each yielding probabilistic information $p(\mathrm{result}|d).$ Our basic question is about finding good strategies to achieve such a goal - or proving lower bounds on the required number of steps for some problems.

As in any optimization problem, it would be nice to have a _convex_ description of the possible strategies and intermediate "states of knowledge", i.e. one such that the acceptable strategies and intermediate states
 1. form a convex subset of a real vector space,
 2. the states are necessary and sufficient to describe the computer's state of knowledge,
 3. if two states of knowledge are attainable by the learner, their convex combinations are attainable as well, and
 4. if two states allow solving some problem, their convex combinations allow solving that problem as well.

This would allow using the powerful methods of [convex optimization and convex
duality](https://web.stanford.edu/~boyd/cvxbook/) (to be more precise, [semidefinite programming](https://en.wikipedia.org/w/index.php?title=Semidefinite_programming&oldid=1092453332)) to obtain lower bounds and algorithms.

But a naive description by conditional probability matrices doesn't fulfill the fourth property. For example, if the learner's internal state was some permutation of the environment's state, each individual state would correspond to complete knowledge, but randomly mixing them yields a state of complete ignorance.

The aim of this note is to find a convex description for classical agents/learners. In contrast to the quantum case, our development doesn't give great bounds on the dimension of the vector space.

## Motivation from quantum query complexity
(Note: One shouldn't need to know anything about quantum physics or query algorithms to follow the rest of this post.)

A great thing about the quantum analogue of this problem (_quantum query algorithms_) is that such a description exists there, namely the Gram matrix of the states for different inputs at a given time. This allowed developments like [Barnum-Saks-Szegedy 2003](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.113.1101&rep=rep1&type=pdf) - and arguably the [adversary bound - query algorithm duality](https://www.cs.umd.edu/~amchilds/qa/qa.pdf), in particular when developed like [here](https://github.com/qudent/RhoPaths).


2. For CPMs $C\_1,C\_2,$ we write that $C\_1\leq C\_2$ iff the agent/learner can transform $C\_2$ into $C\_1$ by a probabilistic transformation without any interaction with the environment. Input and output dimensions are not necessarily equal, and transition probabilities must sum to **at most** $1.$[^3] Clearly, this means that $C\_1$ corresponds to "at most as much knowledge" as $C\_2.$
3. We say $C\_1$ and $C\_2$ are equivalent iff $C\_1\leq C\_2$ and $C\_2\leq C\_1.$ We define the set of **states of knowledge** (SOKs), $\mathbb{K},$ by modding out this equivalence relation from the set of CPMs. Mathematically, $\leq$ was a preorder on the CPMs and becomes a partial order on $\mathbb{K}.$
4. On $\mathbb{K},$ we define multiplication with a nonnegative scalar elementwise, and addition by a **direct sum** of the matrices making up the summands (i.e. taking a disjoint union of the internal memory states, and collecting the conditional probabilities). We can check this behaves well with the equivalence relation. We can also check that addition behaves well with our notion of $\leq,$ i.e. $K\_1\leq K\_2$ iff $K\_1+K_3\leq K\_2+K_3.$ Mathematically, we arrive at a structure called an "[ordered semigroup](https://en.wikipedia.org/w/index.php?title=Ordered_semigroup&oldid=978175111)", which we will turn into an ordered group in step 8. With these definitions, $\mathbb{K}$ is a convex set to which the same intuitions as listed for Gram matrices above apply.[^4]
5. We also define multiplication of two collections by considering the learner to have access to uncorrelated information representing the factors: If the factors are represented by CPMs over internal memory states $a \in A,$ $b \in B,$ the product is represented by a CPM over the Kronecker product $A\times B,$ with the joint probabilities products of the factor probabilities $p((a,b)\mid D)=p(a\mid D)p(b\mid D)$ for $(a,b)\in A\times B.$ Besides behaving well with the equivalence relation, it's also commutative on the SOKs.[^6]


Then in the equivalence classes, $\mathbf{0},\mathbf{1}\in\mathbb{K}$ are neutral w.r.t. addition and multiplication; they correspond to a state of $0$ probability and a state of zero knowledge. We also define $\Omega\in\mathbb{K}$ as the state of complete knowledge.
7. (Note: You can skip reading this item at any time and proceed to the next one.)
   For an example, suppose the learner is in a SOK $K\in\mathbb{K}$ and should make a binary decision which of two experiments to perform next. So it will perform some probabilistic procedure and save the result in an additional register $R'$ with possible states $1,$ $2.$ Then the memory states where $R'=1,$ and the memory states where $R'=2,$ form (non-normalized) SOKs individually that sum to a state obtainable from $K$:

   $K\_1+K\_2\leq K.$

   Now suppose the experiments generate additional data that individually correspond to SOKs $E\_1,E\_2\in \mathbb{K}.$ Then the feasible states of knowledge after one more experiment are given by

   $\\\\\{E\_1 K\_1 + E\_2 K\_2 \mid K\_1 + K\_2 \leq K\\\\\}$

   with $K\_1,K\_2\in\mathbb{K}.$

   Now suppose we have given $K\in\mathbb{K}$ and some utility function $f\colon D\times R\to\mathbb{R}^+ \cup \\\\\{0\\\\\}.$ So the learner outputs some $r\in R,$ and depending on the ground truth $d\in D,$ it gets some utility $f(r,d).$ A special case is computing single- or multi-valued functions (the utility is $1$ if the chosen $r$ was acceptable for $d,$ $0$ otherwise). The vector $\overrightarrow{f(r,\cdot)}$ represents the utilities the learner would obtain if it always chose $r$; we can consider it as a member of $\mathbb{K}$ as above. Then the claim that the learner can obtain a combination of expected utilities $\vec{q}\in(\mathbb{R}^+\cup\\\\\{0\\\\\})^D$ for the various ground truths corresponds to the existence of $K_r\in\mathbb{K}$ for $r\in R$ such that

   $\vec{q}\leq \sum_{r\in R} K_r \overrightarrow{f(r,\cdot)},~~\sum_{r\in R} K_r\leq K.$

   In particular, function evaluation that should succeed with probability at least $1-\epsilon$ for all $d\in D$ corresponds to $\vec{q}=(1-\epsilon)\mathbf{1}.$

   We can chain multiple steps of choosing experiments, starting from complete ignorance, and obtain a convex optimization problem: Suppose we have a (finite for now) set $Q$ of possible experiments, each yielding data $E_q$ for $q\in Q,$ and an initial SOK $K^0\in\mathbb{K}.$ After $T$ steps, a target SOK $\mathbb{K}^T\in\mathbb{K}$ is achievable iff we can find $K^t_q\in\mathbb{K}$ for $0\leq t<T,$ $q\in Q$ such that

   $\sum_{q\in Q} K^0_q \leq K_0,$

   $\sum_{q\in Q} K^t_q\leq\sum_{q\in Q} K^{t-1}_q E_q$ for $1\leq t \leq T-1,$

   $K^T \leq \sum_{q\in Q} K^{T-1}_q E_q.$

   In terms of symbols, this looks analogous to the semidefinite program in Barnum-Saks-Szegedy, equations 9-11 [here](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.113.1101&rep=rep1&type=pdf). The output conditions (the condition that a SOK allows computing some function with a given maximal error), relations 12-13 in that paper, are equivalent to our output conditions as well.

   If such an assignment exists, we can also take a time-average of all the $K^t_q.$ Calling these $\overline{K}_q,$ we find from adding the inequalities above that

   $K^T + \sum\_{q\in Q}\overline{K}\_q\leq K^0 + \sum\_{q\in Q} \overline{K}\_q E\_q.$

   In quantum query complexity, the requirement that such $\overline{K}_q\in\mathbb{K}$ need to exist for SOKs to be transformable in a given number of experiments yields the _adversary bound_. There, one can apply convex duality to the situation: Feasible solutions of the dual problems yield proofs that given SOKs _aren't_ transformable in a certain number of steps, effectively lower bounds for the complexity of solving that problem.

   Assume now that we have found such a collection of $\overline{K}\_q\in\mathbb{K}$ that fulfill the above inequality for some $T.$ Furthermore, assume the algorithm has the option to "do no experiment" as well, i.e. let $\mathbf{1}$ be in the choices of experiments. Then we can "almost solve" the problem of converting $K_0$ to $K_T$ by following a "straight-line path" of intermediate states: The inequality for the $\overline{K}_q$ implies the inequality

   $\frac{K^0 (N-(j+1)) + K^T (j+1)}{N}\mathbf{1} + \frac{1}{N}\sum\_{q\in Q}\overline{K}\_q \leq \frac{K^0 (N-j) + K^T j}{N}\mathbf{1} + \frac{1}{N}\sum\_{q\in Q} \overline{K}\_q E\_q$

   for any natural number $N>0,$ $j\in \\\{0,\ldots,N-1\\\}.$ By the discussion above, these are just the inequalities we need to show that we can transform

   $K_0 + \frac{1}{N}\sum\_{q\in Q}\overline{K}\_q\rightarrow K_T + \frac{1}{N}\sum\_{q\in Q}\overline{K}\_q$

   in $N$ experiments, for any $N,$ where the intermediate states are convex mixtures of the initial and the final state. As we let $N\to \infty,$ this roughly shows that we are "almost" able to transform $K_0$ to $K_T$ with this construction. This is an analogue of the universal algorithm dual of the adversary bound, in the way developed [here](https://github.com/qudent/RhoPaths).

   Things to think about: (Update: I think I may have a solution now. It seems true to me that in both classical and quantum case, whenever $K=K\_1+K\_2=K_A+K_B,$ we can decompose $K=K_{1,A}+K_{1,B}+K_{2,A}+K_{2,B}$ such that $K_{1,A}+K_{1,B}=K\_1,$ $K_{2,A}+K_{2,B}=K\_2,$ $K_{1,A}+K_{2,A}=K_A,$ $K_{1,B}+K_{2,B}=K_B.$ By using that decomposition, we could show that whenever $K^0+\Delta\to K^T+\Delta,$ then $K^0 \to K'^{T},$ $\Delta\to \Delta'$ such that $K'^T+\Delta'=K^T+\Delta.$ Then the rest follows from normalization and inequalities. This could also be applied to the second problem. If it stays correct, I will write more later).
   1. In the quantum case, one can show a concrete bound on how wrong this algorithm would be for some $N$ when applied to some initial state (rather than the shifted initial state) - basically, by a triangle inequality. One can also do this in an ad-hoc way in the classical case. But I don't have a good way to formalize/abstract this in this algebra right now.

   2. It is also weird that this construction needs the algorithm's ability to do nothing. In the classical case, if an algorithm decides it needs to do nothing, it can just perform the next internal processing step instead. I don't know if this is possible coherently in the quantum case, however. It would be nice to be able to put this assertion into the formalism.

9. Together, addition and multiplication allows us to define formal power series of knowledge. For example, suppose our learner observes the stars with a telescope without making any choices. In an infinitesimal time $\Delta t\to 0,$ it observes a supernova with probability $r \Delta t,$ generating experimental data $A\in \mathbb{K}.$ Otherwise, it observes nothing. If $K(t)$ is the state of knowledge over time, we obtain

   $K(t+\Delta t)\to ((1-r \Delta t)\mathbf{1}+r\Delta t A) K(t).$

   This is solved by

   $K(t)=\exp((A-\mathbf{1})rt) K(0),$

   which is to be interpreted as the appropriate formal infinite power series. In fact, writing

   $K(t)=\exp(-rt)\exp(Art)K(0)=\sum^\infty_{k=0} \frac{\exp(-rt) (rt)^k}{k!} A^k K(0)$

   shows that the amount of knowledge obtained follows a Poisson distribution. Note that these equations contain minus signs, so our Grothendieck construction was probably helpful.

## Footnotes
[^1]: We could have defined it by density operators as well, but it seems to me that the formalism introduced later won't easily work that way anymore.
[^2]: With $\delta_{d,e}$ denoting the Kronecker delta, i.e. $\delta_{d,e}=1$ if $d=e$ and $0$ otherwise.
[^3]: Allowing transition probabilities that sum to less than 1 - i.e. allowing to lose probability mass - won't change the equivalence relation/classes, but is helpful for describing the output condition of a query algorithm later.
[^4]: We could have exchanged the order of steps $2$ and $3.$

[^5]: I.e. the vectors which are $1$ on $d$ resp. $d'\in D',$ and $0$ everywhere else.

[^9]: I think one can obtain a more elegant description of possible transformations by adapting the parallel developments by [Gutoski and Watrous](https://arxiv.org/pdf/quant-ph/0611234.pdf) and[Chiribella, D'Ariano, and Perinotti](https://journals.aps.org/pra/abstract/10.1103/PhysRevA.80.022339). But I don't see a use of that as of this note.

[^10]: If one looks into the definitions, one can see that the "states of quasiknowledge" construction is related to a [Grothendieck group construction](https://en.wikipedia.org/wiki/Grothendieck_group).

[^13]: A side remark: $\mathcal{S}_{\mathrm{quant}}\subset\mathcal{S}^\pm_{\mathrm{quant}}$ should be isomorphic to the algebra of complex positive semidefinite $E\times E$ matrices (as a subset of the Hermitian matrices), with elementwise addition and multiplication. One can derive this by considering the [unitary equivalence of purifications of quantum states](https://quantumcomputing.stackexchange.com/questions/9368/why-do-purifications-only-differ-by-a-local-unitary).

[^15]: Although conversely, a preimage of a SOK is _not_ necessarily a SOK. 

[^6]: In the quantum case, this would correspond to Hadamard products of Gram matrices and tensor products of state collections.

[^17]: Apparently, [this Oberwolfach seminar](https://homepages.cwi.nl/~monique/ow-seminar-sdp/) contains a lecture on infinite-dimensional semidefinite optimization.
[^26]: I looked into Wikipedia to read that this has his name from a specific case "which resulted in the development of [K-theory](https://en.wikipedia.org/w/index.php?title=K-theory&oldid=1072713370)". What's written there looks formally quite similar to what I do here, though I don't understand it in detail. So maybe one can call these thoughts "K-theory on the space of probabilistic transformations?"
[^27]: To fulfill the vector space axiom that $\alpha X + \beta X=(\alpha+\beta)X,$ we needed the modding out operation that took us from CPMs to SOKs.
