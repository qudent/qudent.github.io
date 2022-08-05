---
title: 'The \mathbb{R}-algebra of quasiknowledge and convex optimization'
date: 2022-07-25
permalink: /posts/2022/07/convex-knowledge/
tags:
  - cs.ml
  - cs.cc
  - quant-ph
---
(major rewrite+updates ongoing, old version [here](https://github.com/qudent/qudent.github.io/blob/935968fec7d4e89e7953f460d1c2b1093bf0da6b/_posts/2022-07-25-convex-knowledge.md)). The old version is more verbose/philosophical, in this one, I try to be more on-point.
## 1. Abstract
I develop a convex description of a classical or quantum learner's or agent's state of knowledge and environmental state, which would allow using convex optimization and duality on problems related to optimal learning or actions if one restricted to finite-dimensional vector spaces. The physically possible states are described as a convex subset of an associative, commutative $\mathbb{R}$-algebra. This also allows solving formal differential equations and power series that describe e.g. the evolution of a learner's knowledge over time, when it observes a Poisson process generating experimental data.

(Remark: If you don't know quantum physics, you should be able to follow the non-quantum part if you skip the references to that).

## 2. Sets
Consider an agent within an environment described by $e\in E,$ with some internal memory state $m\in M.$
1. In the classical case, we describe the situation by a probability matrix $(p_{e,m})\_{(e,m)\in E\times M}\in(\mathbb{R}^+)^{E\times M},$ where $\mathbb{R}^+$ denotes the set of **nonnegative** real numbers,
2. in the quantum case without decoherence, we have a bipartite quantum state $(\psi_{e,m})\_{(e,m)\in E\times M}\in\mathbb{C}^{E\times M},$ and
3. we model the quantum case with decoherence by adding an additional subsystem $D$ into which the state is supposed to have decohered, and and consider a pure quantum state $(\psi\_{d,e,m})\_{(d,e,m)\in D\times E\times M}\in\mathbb{C}^{D\times E\times M}$ again.[^1]

We will develop these concrete situations and then try to find an axiomatic approach that allows us to reproduce our conclusions.

For a fixed $E,$ we denote and define sets of possible situations
1. $\mathcal{S}'\_{\mathrm{class}}:=\left\\\{(M,P)\mid \left\|M\right\|<\infty, P\in(\mathbb{R}^+)^{E\times M}\right\\\},$
2. $\mathcal{S}'\_{\mathrm{quant}}:=\left\\\{(M,\Psi)\mid |M|<\infty, \Psi\in\mathbb{C}^{E\times M}\right\\\},$
3. $\mathcal{S}'\_{\mathrm{decoh}}:=\left\\\{(D,M,\Psi)\mid \left\|D\right\|<\infty, |M|<\infty, \Psi\in\mathbb{C}^{D\times E\times M}\right\\\}.$

In other words, we do not fix $M$ and $D$ (or limit their sizes), but consider them part of the description. Furthermore, we don't force the classical probability distributions or quantum states to be normalized. We write $\mathcal{S}'$ in developments that apply to any of $\left\\\{\mathcal{S}'\_{\mathrm{class}},\mathcal{S}'\_{\mathrm{quant}},\mathcal{S}'\_{\mathrm{decoh}}\right\\\}.$

We define $\mathcal{S}'^\pm:=\mathcal{S}'\times\mathcal{S}'$ (for classical, coherent-quantum, or decohering-quantum states). Denote a tuple $(S'\_1,S'\_2)\in\mathcal{S}'^\pm$ by $S'\_1-S'\_2$ and interpret it as a formal difference accordingly.

Contained within both $\mathcal{S}'$ and $\mathcal{S}'^\pm$ are distinct elements that correspond to equivalent states of knowledge (e.g. because the agent can transform them without interacting with the environment). We will introduce appropriate equivalence relations in section 5, but first define some more structure on the sets.

## 3. Special elements
With $\vec{p}\in(\mathbb{R}^+)^E$ to be interpreted as a vector of prior probabilities over $E,$ we define $\vec{p}\in\mathcal{S}'$ in the natural ways - as states in which the computer has only one possible internal state:
1. In $\mathcal{S}'\_{\mathrm{class}},$ $\vec{p}:=(\\\{0\\\},\vec{p}),$
2. In $\mathcal{S}'\_{\mathrm{quant}},$ $\vec{p}:=(\\\{0\\\},(\sqrt{p_e})_{(e,0)\in E\times \\\{0\\\}}),$
3. In $\mathcal{S}'\_{\mathrm{decoh}},$ $\vec{p}:=(E,\\\{0\\\},(\delta_{d,e} \sqrt{p_e})_{(d,e,0)\in E\times E\times \\\{0\\\}}).$[^2]
In the coherent quantum case, we take the square root to replace probabilities by amplitude. In the decoherent quantum case, we additionally set $D:=E$ and use a Kronecker delta, enforcing a decoherent probabilistic mixture of initial states. This means that $\vec{p}\in\mathcal{S}'\_{\mathrm{quant}}$ is **not** the same thing as $\vec{p}\in\mathcal{S}'\_{\mathrm{decoh}}.$

In particular, $0,$ $1$ and $d\in E,$ $E'\subseteq E$ are treated as the all-$0,$ all-$1,$ and indicator function vectors (these being generally not normalized probability distributions).[^5] When we have defined addition and multiplication and passed over to equivalence classes, $0$ and $1$ will play the role indicated by their symbol.

By $\Omega\in\mathcal{S}',$ we denote the state of complete knowledge, i.e.
1. $\Omega\in\mathcal{S}'\_{\mathrm{class}}$ and $\Omega\in\mathcal{S}'\_{\mathrm{quant}}$ as $\Omega:=(E,(\delta_{e,m})_{(e,m)\in E\times M}),$ and
2. $\Omega\in\mathcal{S}'\_{\mathrm{decoh}}$ as $\Omega:=(E,E,(\delta_{d,e,m})_{(d,e,m)\in E\times E\times E}).$

Again, note that we defined complete knowledge coherently in $\Omega\in\mathcal{S}'\_{\mathrm{quant}}$ and incoherently in $\mathcal{S}'\_{\mathrm{decoh}}.$
## 4. Set inclusions
Using $0,$ we consider $\mathcal{K'}$ as a subset of $\mathcal{K'}^\pm$ by identifying $K\in\mathcal{K'}$ with $K-0\in\mathcal{K'}^\pm.$ We also consider $\mathcal{S}'\_\mathrm{class}$ and $\mathcal{S}'\_\mathrm{quant}$ as subsets of $\mathcal{S}'\_\mathrm{decoh}$ by treating them as completely decohered and completely undecohered states, respectively - in the first case, we map $(M,P)$ to $(E\times M, \Psi)$ with $\Psi_{(e,m),e',m'}=\delta\_(e,m)\delta\_(e',m')$

## 5. Operations
We now define some operations on the $\mathcal{S}'$ and $\mathcal{S}'^\pm.$
1. Addition, $+,$ is to be interpreted as the environment+agent being in either of the situations that the summands describe, and defined with direct sums:
   1. In $\mathcal{S}'\_{\mathrm{class}},$ $(M\_1,P\_1)+(M\_2,P\_2):=(M\_1\biguplus M\_2, P'),$ with $M\_1\biguplus M\_2$ denoting the disjoint union of $M\_1$ and $M\_2$ and the elements of $P'$ composed of the elements of $P\_1$ and $P\_2.$
   2. In $\mathcal{S}'\_{\mathrm{quant}},$ the definition is equivalent.
   3. In $\mathcal{S}'\_{\mathrm{decoh}},$ $(D\_1,M\_1,\Psi\_1) + (D\_2,M\_2,\Psi\_2) := (D\_1 \biguplus D\_2, M\_1 \biguplus M\_2, \Psi'),$ with the elements of $\Psi'\_{d,e,m}$ composed of those of $\Psi'\_1,\Psi'\_2$ if they $d$ and $m$ belong to the same summand and set to $0$ otherwise.

   In the $\mathcal{S}'^\pm,$ $(A-B) + (C-D) := ((A+C) - (B+D))$ in the natural way.
2. We denote multiplication on the $\mathcal{S}'$ by concatenating the factors and interpret it as the model having access to two pieces of knowledge independently. That is:
   1. In $\mathcal{S}'\_{\mathrm{class}},$ $(M\_1,P\_1)(M\_2,P\_2) := (M\_1 \times M\_2, P')$ with $P'\_{e, (m\_1, m\_2)} := (P\_1)\_{e, m\_1} (P\_2)\_{e, m\_2},$
   2. equivalently in $\mathcal{S}'\_{\mathrm{quant}},$
   3. and in $\mathcal{S}'\_{\mathrm{decoh}},$ $(D\_1,M\_1,\Psi\_1) (D\_2,M\_2,\Psi\_2) := (D\_1 \times D\_2, M\_1 \times M\_2, \Psi')$ with $\Psi'\_{(d\_1, d\_2), e, (m\_1, m\_2)}:= (\Psi\_1)\_{d\_1, e, m\_1}\Psi\_2)\_{d\_2, e, m\_2}.$

   In the $\mathcal{S}'^\pm,$ $(A-B)(C-D) := (AC+BD) - (AD+BC).$
3. When we consider different possible environments, we denote our sets by $\mathcal{S}'^E$ and similar. Considering a bipartite evnvironment $E\times C,$ i.e. a bipartite environment, we define a "partial trace" $\mathrm{tr}\_C \colon \mathcal{S}'^{E\times C}\to \mathcal{S}'^E.$ This works by considering the $C$ register, which previously was part of the environment, as part of the memory: it maps $M\to M\times C,$ copies the entries of $P$ or $\Psi$ and (in the decohering case) leaves $D$ untouched. After modding out an equivalence relation in section 6, this will look more like a partial trace - we "forget" that some information wasn't accessible to the agent before.

   Of course, on $\mathcal{S}'^\pm,$ the $\mathrm{tr}\_C (A-B):= \mathrm{tr}\_C A - \mathrm{tr}\_C B.$
4. We define a preorder $S\_1 \leq S\_2$ to capture the notion that the agent can trivially transform a state into another state, as follows: [^4]
   1. In $\mathcal{S}'\_{\mathrm{class}},$ $(M\_1,P\_2 T) \leq (M\_2,P\_2)$ for all transformation matrices on the memory $T \in {(\mathbb{R}^+)}^{M\_1 \times M\_2}$ with $\Vert T\Vert\_1\leq 1.$ The latter is the [1-norm of $T,$ i.e. the maximal column sum.](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms) This means that starting from any state $m\_1\in M\_1,$ the probabilities of transitioning to a final state must sum to **at most** one; if it is $<1$ for some column, we interpret this as the agent giving up with a certain probability. If _all_ column sums were $1,$ we'd get a "proper" transition matrix that doesn't lose probability mass.
   2. The definition in $\mathcal{S}'\_{\mathrm{quant}}$ is analogous (with $\mathbb{R}^+$ replaced by $\mathbb{C}$) except that our constraint on $T$ is that $\Vert T\Vert\_2\leq 1,$ using the [2-norm of $T$](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms). This means that the maximal singular value of $T$ is $\leq 1.$ Analogously to 1., _all_ singular values being $1$ is equivalent to $T$ being an isometry, i.e. a "proper" quantum transition matrix. If some singular values of $T$ are $<1,$ the block-matrix $\begin{pmatrix}T\\ \sqrt{I-T\_1^\dagger T\_1 T}\end{pmatrix}$ is an isometry nevertheless, so we can consider $T$ to be one [Kraus operator of a quantum channel quantum channel](https://en.wikipedia.org/w/index.php?title=Quantum_operation&oldid=1094787035#Statement_of_the_theorem).
   3. In $\mathcal{S}'\_{\mathrm{quant}},$ we want to consider transitions between both $D$ and the memory. So our transformation is a tuple $(D\_M, T\_M, T\_D)$ with $T\_M \in \mathbb{C}^{M\_2 \times (M\_1 \times D')}$ and $T\_D\in \mathbb{C}^{(D\times D\_M)\times D\_1},$ and $\Vert T\_M\Vert\_2\leq 1,$ $\Vert T\_D\Vert\_2\leq 1.$ So $T\_M$ transforms the memory and generates a register $D\_M$ that becomes part of the decohering space, and $T\_E$ is then an arbitrary transformation of that decohering space, which is **not necessarily an isometry either**. 
   We denote the application of both as $T\_D(\Psi\_2 T\_M))$; in conclusion, our inequalities are $(D\_1,M\_1, T\_D(\Psi\_2 T\_M)) \leq (D\_2, M\_2, \Psi\_2)$. [^9]

   On $\mathbb{S}'^\pm,$ $(A-B)\leq (C-D)$ iff $A+D\leq B+D.$
5. Finally, we define an **equivalence relation** on the $\mathcal{S}'$ and $\mathcal{S}'^\pm$ by $X\sim Y\leftrightarrow (X\leq Y \wedge Y\leq X)$, and call the equivalence classes the spaces of **states of knowledge** $\mathcal{S}$ and **states of quasiknowledge** $\mathcal{S}^\pm$. The "states of quasiknowledge" construction is related to a [Grothendieck group construction](https://en.wikipedia.org/wiki/Grothendieck_group).

   It is tedious but straightforward to check that all our operations behave well with this equivalence relation. What's more, by definition, $\leq$ becomes a **partial order**, and $\mathcal{S}\subseteq\mathcal{S}^\pm$ a **convex subset of an associative, commutative $\mathbb{R}$-algebra**.
1.5. remark: too many states now, but first define ops, then equivalence classes


3. s'-> s, dann gleich "ops funktionieren immer noch"
5. s_pm'->s_pm

## Transformation and equivalence relations

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

8. Our $+$ and $0$ yield a commutative monoid, i.e. we can add and have a neutral element, but can't yet subtract. A [_Grothendieck group construction_](https://en.wikipedia.org/w/index.php?title=Grothendieck_group&oldid=1091622036)[^26] allows us to turn this monoid into a commutative group. We obtain an $\mathbb{R}$-algebra, which is also a vector space.[^27] Our original set of "physical" states of knowledge is still a convex subset of that vector space. So if we find or truncate to a finite basis, we can hopefully do convex optimization and duality over it.
9. Together, addition and multiplication allows us to define formal power series of knowledge. For example, suppose our learner observes the stars with a telescope without making any choices. In an infinitesimal time $\Delta t\to 0,$ it observes a supernova with probability $r \Delta t,$ generating experimental data $A\in \mathbb{K}.$ Otherwise, it observes nothing. If $K(t)$ is the state of knowledge over time, we obtain

   $K(t+\Delta t)\to ((1-r \Delta t)\mathbf{1}+r\Delta t A) K(t).$

   This is solved by

   $K(t)=\exp((A-\mathbf{1})rt) K(0),$

   which is to be interpreted as the appropriate formal infinite power series. In fact, writing

   $K(t)=\exp(-rt)\exp(Art)K(0)=\sum^\infty_{k=0} \frac{\exp(-rt) (rt)^k}{k!} A^k K(0)$

   shows that the amount of knowledge obtained follows a Poisson distribution. Note that these equations contain minus signs, so our Grothendieck construction was probably helpful.

10. We discussed classical probability theory so far. But I think this works for both pure and mixed quantum theory as well, though I haven't thoroughly worked through the details and don't want to make a definite claim - it would be really nice to get an adversary method for faulty query algorithms though:
	1. For pure quantum theory, the CPMs correspond to collections of wavefunctions for $d\in D,$ and the $\leq$ relation corresponds to applying unitaries and projectors. Then the convex space _should_ be equivalent to the space of Gram matrices (of complex vectors), and the Grothendieck construction should yield the space of Hermitian matrices. In this equivalence, addition and multiplication correspond to elementwise addition and multiplication of these Gram matrices. (Note that, when going from collections of states to Gram matrices, direct sums turn into sums and tensor products turn into elementwise products).
	2. We represent mixed quantum theory using purifications: The analogue of the CPMs are pure quantum vectors including a subsystem representing the environment, and the equivalence relation we mod out over includes local operations on the environment.

## More details
### The set-up/CPMs
Suppose the learner has stored its previous (probabilistic) knowledge as a state of its internal memory $x\in X.$ Consider the _conditional probability matrix_ (CPM) $C=(p(x\mid d))\_{x\in X,d\in D}\in\mathbb{R}^{X\times D}.$ Then $C$ is sufficient (but, as we'll see, not necessary) to describe the "knowledge" the learner gathers about the environment for any $d\in D.$ For example, if the learner's ultimate goal is to calculate some function $f\colon D\to R,$ it does (and has to do) so by applying some probabilistic process $X\to R,$ with some transition matrix $T=(p(r\mid x))\_{r\in R,x\in X}.$ The appropriate matrix entries of $TC$ contain the probabilities $p(r=f(d)\mid d),$ the success probabilities of that procedure for given $d\in D.$

We formally _don't_ require that the conditional probabilities sum to $1,$ but do require that they're nonnegative. If the conditional probabilities sum to $<1$ for some $d\in D,$ we can interpret this as a learner that has given up on a problem by a certain time, with some probability.

### Preorder on transition matrices by transformability
On the set of CPMs, $\leq$ as discussed in the sketch is a _preorder_ - for any $C,C\_1,C\_2,C_3,$ it fulfills
- transitivity: $C\_1\leq C\_2$ and $C\_2\leq C_3$ implies $C\_1\leq C_3,$ and
- reflexivity: $C\leq C.$

### $\leq$ isn't a partial order yet
But $\leq$ is _not_ a partial order, as it does not fulfill symmetry: $C\_1\leq C\_2$ and $C\_2\leq C\_1$ does not necessarily imply $C\_1=C\_2.$ Two counterexamples:
   - Permuting the rows of a conditional probability matrix $M,$ corresponding to changing the labeling of $x\in X,$
   - Replacing some $x\in X$ with one of two additional states $x\_1,$ $x\_2$ with probability $1/2$ each.

Intuitively, this means that, to describe the "state of knowledge" of the learner, the CPMs contain superfluous information. We can define an equivalence relation on the CPMS to capture this, with $C\_1\sim C\_2$ iff $C\_1\leq C\_2 \wedge C\_2\leq C\_1.$ We **define our set of _states of knowledge (SOKs)_** $\mathbb{K}$ as a mathematical object as the set of equivalence classes under this equivalence relation. We will now argue that $K\_1= K\_2\in \mathbb{R}$ iff these "states of knowledge" should philosophically be considered equivalent:

It is clear that  $K\_1=K\_2$ implies they should be considered equivalent. Conversely, suppose that two CPMs $C\_1,C\_2$ should be considered equivalent. This means that, given $C\_2,$ the learner can solve all tasks it could solve when given $C\_1$ and vice versa. In particular, it can emit a probability distribution corresponding to $C\_1.$ But this means that $C\_1\leq C\_2.$ Similarly, $C\_2\leq C\_1$ as well, so $C\_1$ and $C\_2$ are in the same equivalence class.

By standard math, for $K\_1, K\_2\in \mathbb{K},$ $K\_1\leq K\_2$ is independent of the choice of representative, and $\leq$ is a partial order on $\mathbb{K}.$

### Convexity of states of knowledge
We define addition and scalar multiplication operations on states of knowledge as in step 4 of the sketch. We can easily verify they behave well with the equivalence relation.

A convex combination $p K\_1+(1-p)K\_2$ now corresponds to a SOK in which, before performing the experiments leading up to some state of knowledge, the learner had randomly decided to perform a procedure leading to $K\_1$ with probability $p,$ and a procedure leading to $K\_2$ with probability $1-p$ (and stored the choice it made). So our definition of addition brings us one step closer to being able to use convex optimization to optimize over states of knowledge:
- When $K\_1$ and $K\_2$ are feasible in some scheme, their convex combinations are feasible as well,
- and when $K\_1$ and $K\_2$ are both individually acceptable for some task (i.e. yield a low enough achievable failure probability), their convex combinations are jointly acceptable as well (as one can achieve the appropriate convex mixtures of failure probabilities).

As mentioned in the introduction, if we just took convex combinations of our original CPMs, that wouldn't be true.

### The Grothendieck group
$(\mathbb{K},+)$ forms a commutative _monoid_: There is a neutral element $0\in \mathbb{K},$ represented by any all-$0$ matrix, and addition is associative and commutative. We can turn this into a group, i.e. add enough elements for there to be inverses, by a [_Grothendieck group construction_](https://en.wikipedia.org/w/index.php?title=Grothendieck_group&oldid=1091622036).[^26] This works as follows:

1. We are given a commutative monoid $(M,+),$
2. We define the group by $M_\pm:=M\times M/\sim,$ where $(m\_1,m\_2)\sim(m_3,m_4)$ iff $m\_1+m_4=m\_2+m_3.$ In words, a tuple $(m\_1,m\_2)$ is to be interpreted as the group element $m\_1-m\_2,$ and we can determine equivalence of two group elements $m\_1-m\_2, m_3-m_4$ by checking whether $m\_1+m_4=m_3+m\_2.$
3. We define $(m\_1,m\_2)+(m_3,m_4):=(m\_1+m_3,m\_2+m_4),$ and the inverse of an element $M$ represented by $(m\_1,m\_2)$ is denoted by $-M$ and represented by $(m\_2,m\_1).$

For our monoid $(\mathbb{K},+),$ we call elements of the resulting set $\mathbb{K}_\pm$ _quasistates of knowledge_. The point of our exercise is that these form a vector space over $\mathbb{R},$ if we define scalar multiplication in the natural way ($\alpha (K\_1,K\_2):=(\alpha K\_1, \alpha K\_2)$ for $\alpha\geq 0,$ and $\alpha K:=(-\alpha) (-K)$ for $\alpha<0$).[^27]

We can also define multiplication on $\mathbb{K}\_\pm.$ Keeping in mind that $(K\_1,K\_2)$ is to be interpreted as $K\_1-K\_2,$ our definition is $(K\_1,K\_2)(K_3,K_4):=(K\_1K_3+K\_2K_4,K\_2K_3+K\_1K_4).$ We arrive at a commutative $\mathbb{R}$-algebra $\mathbb{K}\_\pm,$ with the "physical" SOKs $\mathbb{K}\subset\mathbb{K}\_\pm$ a convex subset of $\mathbb{K}\_\pm.$ Finally, $(K\_1,K\_2)\leq(K_3,K_4)$ iff $K\_1+K_4\leq K_3+K\_2.$ This turns our thing into an ordered group, which seems to behave well with standard laws of school arithmetic to me.

Any $\mathbb{R}$-algebra is an $\mathbb{R}$-vector space, but the dimension of the space will probably be infinite in general. Textbook convex optimization is done in Euclidean space (i.e. $\mathbb{R}^n$ with $n$ finite);[^17] we can truncate our space to a finite basis, and for a given finite set of possible experiments taking values in a finite set, we should be able to find a basis that covers any given finite number of observations. But I don't fully understand whether/to what extent the nice ideas of duality and similar would still work in our situation.

## Footnotes
[^1]: We could have defined it by density operators as well, but it seems to me that the formalism introduced later won't easily work that way anymore.
[^2]: With $\delta_{d,e}$ denoting the Kronecker delta, i.e. $\delta_{d,e}=1$ if $d=e$ and $0$ otherwise.
[^3]: Allowing transition probabilities that sum to less than 1 - i.e. allowing to lose probability mass - won't change the equivalence relation/classes, but is helpful for describing the output condition of a query algorithm later.
[^4]: We could have exchanged the order of steps $2$ and $3.$

[^5]: I.e. the vectors which are $1$ on $d$ resp. $d'\in D',$ and $0$ everywhere else.

[^9]: I think one can obtain a more elegant description of possible transformations by adapting the parallel developments by [Gutoski and Watrous](https://arxiv.org/pdf/quant-ph/0611234.pdf) and[Chiribella, D'Ariano, and Perinotti](https://journals.aps.org/pra/abstract/10.1103/PhysRevA.80.022339). But I don't see a use of that as of this note.

[^6]: In the quantum case, this would correspond to Hadamard products of Gram matrices and tensor products of state collections.

[^17]: Apparently, [this Oberwolfach seminar](https://homepages.cwi.nl/~monique/ow-seminar-sdp/) contains a lecture on infinite-dimensional semidefinite optimization.
[^26]: I looked into Wikipedia to read that this has his name from a specific case "which resulted in the development of [K-theory](https://en.wikipedia.org/w/index.php?title=K-theory&oldid=1072713370)". What's written there looks formally quite similar to what I do here, though I don't understand it in detail. So maybe one can call these thoughts "K-theory on the space of probabilistic transformations?"
[^27]: To fulfill the vector space axiom that $\alpha X + \beta X=(\alpha+\beta)X,$ we needed the modding out operation that took us from CPMs to SOKs.
