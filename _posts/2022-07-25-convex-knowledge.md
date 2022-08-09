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
I develop a convex description of a classical or quantum learner's or agent's state of knowledge and environmental state, which would allow using convex optimization and duality on problems related to optimal learning or actions if one restricted to finite-dimensional vector spaces. The physically possible states are described as a convex subset of an associative, commutative, ordered $\mathbb{R}$-algebra. This also allows solving formal differential equations and power series that describe e.g. the evolution of a learner's knowledge over time when it observes a Poisson process generating experimental data.

The **outline**: In sections 2-5, I develop the concrete situations of classical, pure quantum, and mixed quantum physics and obtain structures $\mathcal{S}^\pm_{\mathrm{class}},$ $\mathcal{S}^\pm_{\mathrm{quant}},$ $\mathcal{S}^\pm_{\mathrm{decoh}}.$ More precisely, section 2 defines sets, sections 3-4 structure on that sets, and section 5 an equivalence relation that makes equivalent "states of quasiknowledge" equal. Then section 5 mentions the algebraic axioms that these structures fulfill. The remaining sections discuss applications.

**Remark:** If you want to speed up reading or don't know quantum physics, you can skip all mentions of non-classical situations.

## 2. Sets before modding out the equivalence relation
Consider an agent within an environment that could be in a state $e\in E,$ with some internal memory state $m\in M.$

1. In the classical case, we describe the situation by a probability matrix $(p_{e,m})_{(e,m)\in E\times M}\in(\mathbb{R}^+)^{E\times M},$ where $\mathbb{R}^+$ denotes the set of **nonnegative** real numbers,
2. in the quantum case without decoherence, we have a bipartite quantum state $(\psi_{e,m})_{(e,m)\in E\times M}\in\mathbb{C}^{E\times M},$ and
3. we model the quantum case with decoherence by adding an additional subsystem $D$ into which the state is supposed to have decohered, and and consider a pure quantum state $(\psi_{d,e,m})_{(d,e,m)\in D\times E\times M}\in\mathbb{C}^{D\times E\times M}$ again.[^1]

For a fixed $E,$ we denote and define sets of possible situations (which will turn into our desired notions of "states of knowledge" after modding out equivalence relation in section 5):

1. $\mathcal{S}'_{\mathrm{class}}:=\left\{(M,P)\mid |M|<\infty, P\in(\mathbb{R}^+)^{E\times M}\right\},$
2. $\mathcal{S}'_{\mathrm{quant}}:=\left\{(M,\Psi)\mid |M|<\infty, \Psi\in\mathbb{C}^{E\times M}\right\},$
3. $\mathcal{S}'_{\mathrm{decoh}}:=\left\{(D,M,\Psi)\mid |D|<\infty, |M|<\infty, \Psi\in\mathbb{C}^{D\times E\times M}\right\}.$

In other words, we do not fix $M$ and $D$ (or limit their sizes beyond requiring they are finite), but consider them part of the description. Furthermore, we don't force the classical probability distributions or quantum states to be normalized. We will write $\mathcal{S}'$ in arguments that work for any of the sets $\mathcal{S}'_{\mathrm{class}},$ $\mathcal{S}'_{\mathrm{quant}},$ $\mathcal{S}'_{\mathrm{decoh}}.$ If we refer to a particular $E$, we write $\mathcal{S}'^E.$

We define $\mathcal{S}'^\pm:=\mathcal{S}'\times\mathcal{S}'$ (for classical, coherent-quantum, or decohering-quantum states). Denote a tuple $(S'_1,S'_2)\in\mathcal{S}'^\pm$ by $S'_1-S'_2$ and interpret it as a formal difference accordingly.

As discussed in the outline, we spend the next sections defining more structure on the sets before modding out the right equivalence relation in section 6.

## 3. Set inclusions (injective maps)
We define maps that will play the role of inclusion maps after our equivalence relation (i.e. allow us to consider some sets as subsets of other sets - will be/stay injections and form nothing but commutative diagrams when concatenated):

### 3.1. Classical and pure quantum as mixed quantum states
The inclusions are

1. $\mathcal{S}'_\mathrm{class}\to\mathcal{S}'_\mathrm{decoh},$ $(M,P)\to(E\times M, (\delta_{(e,m)}\delta_{(e',m')}\sqrt{P_{e,m}})_{(e,m),e',m'})$ (in words, treating a classical as a completely decohered state),
2. $\mathcal{S}'_\mathrm{quant}\to\mathcal{S}'_\mathrm{decoh},$ $(M,\Psi)\to(\{0\}, (\sqrt{P_{e,m}})_{0,e,m})$ (in words, treating a pure quantum state as a quantum state with no decoherence).

### 3.2. Knowledge as quasiknowledge
$\mathcal{S}'\to\mathcal{S}'^\pm,$ $S'\to S'-0$.

### 3.2. Elements of E
We want to consider $e\in E$ as a state in which the environment is guaranteed to be in state $e.$ 

1. $E\to\mathcal{S}'_\mathrm{class}$, $e\to(\{0\},(\delta_{e',e})_{(e',0)\in E\times \{0\}}))$

(where $\{0\}$ is an arbitrary 1-element memory space). The situation is completely analogous in the pure-quantum or mixed-quantum case (in the latter, with a 1-dimensional decohering space).

### 3.3. Real numbers

$\mathbb{R}^+\to \mathcal{S}'_\mathrm{class}$ maps $r$ to an all-$r$ matrix, again with trivial memory space. $\mathbb{R}^+\to  \mathcal{S}'_\mathrm{quant},$ $\mathcal{S}'_\mathrm{decoh}$ map to an all-$\sqrt{r}$ matrix with trivial memory (and decohering) space.

We embed real numbers $-r'<0$ in $\mathcal{S}'^\pm$ as $0-r'$, i.e. as the negation of the embedding of $r'$.

## 4. Operations
We now define some operations on the $\mathcal{S}'$ and $\mathcal{S}'^\pm.$

1. Addition, $+,$ is to be interpreted as the environment+agent being in either of the situations that the summands describe, and defined with direct sums:
   1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_1)+(M_2,P_2):=(M_1\biguplus M_2, P'),$ with $M_1\biguplus M_2$ denoting the disjoint union of $M_1$ and $M_2$ and the elements of $P'$ composed of the elements of $P_1$ and $P_2.$
   2. In $\mathcal{S}'_{\mathrm{quant}},$ the definition is equivalent.
   3. In $\mathcal{S}'_{\mathrm{decoh}},$ $(D_1,M_1,\Psi_1) + (D_2,M_2,\Psi_2) := (D_1 \biguplus D_2, M_1 \biguplus M_2, \Psi'),$ with the entries of $\Psi'$ taken from $\Psi'_1,\Psi'_2$ if $d$ and $m$ belong to the same summand and set to $0$ otherwise.

   In the $\mathcal{S}'^\pm,$ $(A-B) + (C-D) := ((A+C) - (B+D))$ as expected.
2. We denote multiplication on the $\mathcal{S}'$ by concatenating the factors and interpret it as the model having access to two pieces of knowledge independently. That is:
   1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_1)(M_2,P_2) := (M_1 \times M_2, P')$ with $P'_{e, (m_1, m_2)} := (P_1)_{e, m_1} (P_2)_{e, m_2},$
   2. equivalently in $\mathcal{S}'_{\mathrm{quant}},$
   3. and in $\mathcal{S}'_{\mathrm{decoh}},$ $(D_1,M_1,\Psi_1) (D_2,M_2,\Psi_2) := (D_1 \times D_2, M_1 \times M_2, \Psi')$ with $\Psi'_{(d_1, d_2), e, (m_1, m_2)} := (\Psi_1)_{d_1, e, m_1}(\Psi_2)_{d_2, e, m_2}.$

   In the $\mathcal{S}'^\pm,$ $(A-B)(C-D) := (AC+BD) - (AD+BC).$
3. Remember that when we consider different possible environments, we denote our sets by $\mathcal{S}'^E$ and similar. Considering a bipartite environment $E\times C,$ we define a "partial trace" $\mathrm{tr}_C \colon \mathcal{S}'^{E\times C}\to \mathcal{S}'^E.$ This works by considering the $C$ register, which previously was part of the environment, as part of the memory: it maps $M\to M\times C,$ copies the entries of $P$ or $\Psi$ and (in the decohering case) leaves $D$ untouched. After modding out an equivalence relation in section 6, this will look more like the partial trace  - after making some information accessible to the agent, the state of knowledge becomes equivalent with respect to all transformations that it can perform on that information.

   Of course, on the $\mathcal{S}'^\pm,$ the $\mathrm{tr}_C (A-B):= \mathrm{tr}_C A - \mathrm{tr}_C B.$

   The partial trace will be useful to discuss the interface between agent and the environment, i.e. discuss the possibility that the agent makes observations or performs actions that influence the next system state.
   
4. We define a preorder $S_1 \leq S_2$ to capture the notion that the agent can trivially transform a state into another state, as follows:
   1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_2 T) \leq (M_2,P_2)$ for all transformation matrices on the memory $T \in {(\mathbb{R}^+)}^{M_1 \times M_2}$ with $\Vert T\Vert_1\leq 1.$ The latter is the [1-norm of $T,$ i.e. the maximal column sum.](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms) This means that starting from any state $m_2\in M_2,$ the probabilities of transitioning to a final state must sum to **at most** one; if it is $<1$ for some column, we interpret this as the agent giving up with a certain probability. If _all_ column sums were $1,$ we'd get a "proper" transition matrix that doesn't lose probability mass.
   2. The definition in $\mathcal{S}'_{\mathrm{quant}}$ is analogous (with $\mathbb{R}^+$ replaced by $\mathbb{C}$) except that our constraint on $T$ is that $\Vert T\Vert_2\leq 1,$ using the [2-norm of $T$](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms). This means that the maximal singular value of $T$ is $\leq 1.$ Analogously to 1., _all_ singular values being $1$ is equivalent to $T$ being an isometry, i.e. a "proper" quantum transition matrix. If some singular values of $T$ are $<1,$ the block-matrix $\begin{pmatrix}T\\\\ \sqrt{I-T^\dagger  T}\end{pmatrix}$ is an isometry nevertheless; we can imagine the agent gives up if it lands in the lower block of that matrix and/or consider $T$ to be one [Kraus operator of a quantum channel](https://en.wikipedia.org/w/index.php?title=Quantum_operation&oldid=1094787035#Statement_of_the_theorem).
   3. In $\mathcal{S}'_{\mathrm{decoh}},$ we want to consider transitions involving both $D$ and the memory. So we have $(D_1,M_1, T_D(\Psi_2 T_M)) \leq (D_2, M_2, \Psi_2),$ where $T_D(\Psi_2 T_M))$ denotes
	   1. The application of some $T_M \in \mathbb{C}^{M_2 \times (M_1 \times D_M)}$ to the memory space of $\Psi_2,$ where $D_M$ is an intermediate decohering space and $\Vert T_M\Vert_2\leq 1,$
	   2. following that, the application of $T_D\in \mathbb{C}^{D_1\times (D\times D_M)}$ to $D$ and $D_M$, with $\Vert T_D\Vert_2\leq 1$ as well. So $T_M$ transforms the memory and generates a register $D_M$ that becomes part of the decohering space, and $T_D$ is an arbitrary transformation of that decohering space, which is **not necessarily an isometry either**.[^9]

   On $\mathbb{S}'^\pm,$ $(A-B)\leq (C-D)$ iff $A+D\leq B+D.$

## 5. The equivalence relation and the algebra
Finally, we define an **equivalence relation** on the $\mathcal{S}'$ and $\mathcal{S}'^\pm$ by $X\sim Y\leftrightarrow (X\leq Y \wedge Y\leq X)$, and call the equivalence classes the spaces of **states of knowledge** $\mathcal{S}$ and **states of quasiknowledge** $\mathcal{S}^\pm$.[^10]

It is tedious but straightforward to check (fingers crossed) that all our operations behave well with this equivalence relation and $\mathcal{S}\subseteq\mathcal{S}^\pm$ becomes a **convex subset of an associative, commutative ordered $\mathbb{R}$-algebra**.[^13]

The partial trace $\mathrm{tr}_C\colon\mathcal{S}^{\pm (E\times C)}\to \mathcal{S}^{\pm E}$ is a **linear map** that surjectively maps states of knowledge to states of knowledge (i.e. the image $\mathrm{tr}_C(\mathcal{S}^{E\times C})=\mathcal{S}^{E}$). Conversely, however, the preimage of a SOK is _not_ necessarily a SOK. $\mathrm{tr}_C$ is also monotonous in $\leq$.

Note that after the equivalence relation, the set inclusion $\mathbb{R}\subseteq \mathcal{S}^{\pm\{0\}}$ defined in 3.3. is actually _surjective_ for a 1-element $E=\{0\}$. So it makes sense to define a "total trace" $\mathrm{tr}\colon \mathcal{S}^{\pm}\to\mathbb{R}$, which treats the environment $E$ as $\{0\}\times E$ and calculates the partial trace over $E$. Interpreting the result as an element of $\mathbb{R},$ this returns the $1$-norm/squared $2$-norm of a SOK.


## 6. An agent interacting with its environment/convex optimization
### 6.1. Evolution
Now consider an agent interacting with its environment. So we introduce some output and input subsystems $O,$ $I,$ and a "law of nature" $T\colon\mathcal{S}^{E\times O}\to \mathcal{S}^{E\times I}$ that evolves the environment influenced by the agent's choices, and generating measurement data. The "law of nature" should be instantiated as some transition matrix $T'$, similar to the ones in our order definition in 4.4.:

1. For $\mathcal{S}_\mathrm{class},$ $T'\in(\mathbb{R}^+)^{(E\times I)\times(E\times O)}$ and $\Vert T'\Vert_1\leq 1,$
2. For $\mathcal{S}_\mathrm{quant},$ $T'\in\mathbb{C}^{(E\times I)\times(E\times O)}$ and $\Vert T'\Vert_2\leq 1,$
3. For $\mathcal{S}_\mathrm{decoh},$ $T'\in\mathbb{C}^{(D_T\times E\times I)\times(E\times O)}$ and $\Vert T'\Vert_2\leq 1,$ i.e. $T'$ may add some new space $D_T$ to the decohering space.

Starting with a SOK $S\in \mathcal{S}^{E},$ the states of knowledge attainable by the agent in one step are then

$\left\{ \mathrm{tr}_I (T(S_O)) \mid\mathrm{tr}_O S_O \leq S, S_O\in \mathcal{S}^{E\times O} \right\}:$

The agent is able to apply an arbitrary transformation to its internal state to write something in $O,$ which corresponds to creating an arbitrary SOK $S_O$ whose partial trace is at most $S.$ Then the transformation corresponds to applying T, and reading of the input is application of the partial trace.

### 6.2. Measurements
Now suppose that given $S\in\mathcal{S}^E,$ the agent wants to calculate a function $f\colon E\to C.$ As before, it produces some $S_C\in \mathcal{S}^{E\times C}$ with $\mathrm{tr}_C S_C \leq S$. Given this $S_C,$ the "probability" (in scare quotes because $S$ doesn't need to be normalized) of getting output $C$ for input $E$ is $\mathrm{tr}((E,C) S_C)$ (with $(E,C)$ interpreted as a member of $\mathcal{S}^{E\times C}$ as in 3.2): Multiplying by $(E,C)$ turns all elements to $0$ that don't correspond to that combination, and $\mathrm{tr}$ calculates the remaining probability.

We can easily turn this into some output condition, e.g. the condition that a SOK needs to allow the agent to calculate a function with a given maximal error probability. What's more, these are **convex** conditions because the SOKs are convex and partial traces are linear. So when we **truncate to a finite basis, we should be able to use convex optimization and duality** [^17] to find optimal algorithms that allow the agent to determine some value or perform some manipulation of the environment. This is a generalization of [Barnum-Saks-Szegedy](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.113.1101&rep=rep1&type=pdf).

TODO: We can also generalize the adversary bound.

## 7. Formal power series of knowledge
Together, addition and multiplication allows us to define formal power series of knowledge (we leave mathematical rigour for a moment, as we didn't define infinite sums). For example, suppose our learner observes the stars with a telescope without making any choices. In an infinitesimal time $\Delta t\to 0,$ it observes a supernova with probability $r \Delta t,$ generating experimental data $A\in \mathcal{S}.$ Otherwise, it observes nothing. If $S(t)$ is the state of knowledge over time, we obtain

   $S(t+\Delta t)\to ((1-r \Delta t)\mathbf{1}+r\Delta t A) S(t).$

   This is solved by

   $S(t)=\exp((A-\mathbf{1})rt) S(0),$

   which is to be interpreted as the appropriate formal infinite power series. In fact, writing

   $K(t)=\exp(-rt)\exp(Art)S(0)=\sum^\infty_{k=0} \frac{\exp(-rt) (rt)^k}{k!} A^k S(0)$

   shows that the amount of knowledge obtained follows a Poisson distribution. Note that these equations contain minus signs, so our Grothendieck construction was probably helpful.
   
   TODO: Maybe we can also define such a formal power series when the agent is allowed to make choices - when considering _sets of attainable states of knowledge_ instead of _individual states of knowledge_.

## Footnotes
[^1]: We could have defined it by density operators as well, but it seems to me that the formalism introduced later won't easily work that way anymore.

[^9]: I think one can obtain a more elegant description of possible transformations by adapting the parallel developments by [Gutoski and Watrous](https://arxiv.org/pdf/quant-ph/0611234.pdf) and [Chiribella, D'Ariano, and Perinotti](https://journals.aps.org/pra/abstract/10.1103/PhysRevA.80.022339). But I don't see a use of that as of this note.

[^10]: If one looks into the definitions, one can see that the "states of quasiknowledge" construction is related to a [Grothendieck group construction](https://en.wikipedia.org/wiki/Grothendieck_group). I looked into Wikipedia to read that this has his name from a specific case "which resulted in the development of [K-theory](https://en.wikipedia.org/w/index.php?title=K-theory&oldid=1072713370)". What's written there looks formally quite similar to what I do here, though I don't understand it in detail. So maybe one can call these thoughts "K-theory on the space of probabilistic transformations?"

[^13]: A side remark: $\mathcal{S}_{\mathrm{quant}}\subset\mathcal{S}^\pm_{\mathrm{quant}}$ should be isomorphic to the algebra of complex positive semidefinite $E\times E$ matrices (as a subset of the Hermitian matrices), with elementwise addition and multiplication. One can derive this by considering the [unitary equivalence of purifications of quantum states](https://quantumcomputing.stackexchange.com/questions/9368/why-do-purifications-only-differ-by-a-local-unitary).

[^17]: Apparently, [this Oberwolfach seminar](https://homepages.cwi.nl/~monique/ow-seminar-sdp/) contains a lecture on infinite-dimensional semidefinite optimization.