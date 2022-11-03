<meta charset="utf-8">

   **The $\mathbb{R}$-algebra of quasiknowledge and convex optimization**
                          *2022-08-09*

permalink: /posts/2022/08/convex-knowledge/

In a bid to apply methods from convex optimization and duality to optimal learning/control problems, I develop a convex description of a classical or quantum learner's or agent's state of knowledge about its evironment.

tags:
  - cs.ml
  - cs.cc
  - quant-ph

# Intuition and motivation
In the foundations of quantum physics, [generalized probabilistic theories](https://en.wikipedia.org/w/index.php?title=Generalized_probabilistic_theory&oldid=1095657221) are a family of mathematical structures that generalize both quantum physics and classical probability theory, and -- to a limited degree -- allow reasoning about quantum and classical systems using the same algebraic manipulations. The aim of this note is to develop a similarly general structure to describe the notion of _an agent's knowledge about its environment, and the actions it can perform to learn about and manipulate that environment_. For example, this framework could be applicable if one **trains a machine learning model and wants to determine the "most useful questions" to ask human labelers or the real-world environment,** if compute is much cheaper than gathering that data. I'll focus on situations where the agent has **unbounded internal computational power,** and is only restricted by the interface with the environment.

In probability theories, one would describe the "state of knowledge" by a joint probability distribution over the environment's and the agent's internal memory states. The approach I present is different in **two basic ways:**

1. **Equivalent joint probability distributions are the same state of knowledge:** In terms of "how much the agent knows about its environment", many joint probability distributions should be considered equivalent --- for example, if the environment and the internal memory are both described by one bit, the joint probability matrices[^1] $\begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix}$ and $\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}$ both correspond to the agent having complete knowledge of the environment. We will formalize this idea by an equivalence relation over which we take equivalence classes --- essentially letting states be equivalent if the agent can transform them into each other without interacting with the environment. Then only distinct equivalence classes correspond to distinct "states of knowledge".
2. **The addition used for convex mixtures is a direct, not entrywise sum:** A basic feature of classical probability distributions and mixed quantum states is _convexity_: If $A$ and $B$ are valid probability distributions, transition matrices or similar, and $0\leq p \leq 1,$ an object denoted by $c_p(A,B):=pA+(1-p)B$ exists and is to be interpreted as "the result of choosing $A$ with probability $p$ and $B$ with probability $1-p$". In probability theory, $A$ and $B$ are some vectors or matrices, $pA$ denotes the **entrywise** product with $p,$ and the $+$ sign denotes the **entrywise sum** of these objects.

   In our notion of "states of knowledge", a "convex mixture" $c_p(A,B)=pA+(1-p)B$ should correspond to the agent "being in state of knowledge $A$ with probability $p,$ and in state of knowledge $B$ with probability $1-p.$" **That wouldn't work if we just took elementwise convex combinations of the joint probability distributions:** Reusing the example from 1., $\frac{1}{2} \begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix}+\frac{1}{2}\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}=\begin{pmatrix}0.25 & 0.25\\0.25 & 0.25\end{pmatrix}$ corresponds to a state of complete ignorance even though the constituent matrices correspond to perfect knowledge. We need the system to remember _which_ constituent matrix was chosen. To do this, we use **direct sums:** Interpreting $\oplus$ as a direct sum of the columns,

   $$\frac{1}{2} \begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix} \oplus \frac{1}{2}\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}=
\begin{pmatrix}
0.25 & 0    &    0 & 0.25\\
0    & 0.25 & 0.25 &    0
\end{pmatrix}.$$

   We interpret the right-hand side's memory space as follows: It contains an additional bit storing whether the knowledge is encoded as in the left or as in the right summand. It will turn out that the right-hand side matrix is the state of complete knowledge as well --- in other words, another representative of the equivalence class that contains $\begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix}$ and $\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}$. This is as it should be, as having complete knowledge in either of two ways, with probability $1/2$ each, is equivalent to having complete knowledge with probability $1.$
   
   So in our formal algebraic structure of "states of knowledge" describing classical physics, multiplication with a nonnegative scalar is defined elementwise, and addition is implemented by a direct sum. If we find a way to subtract states of knowledge as well (or multiply with a negative scalar), we have all the ingredients needed for a real vector space (if everything fulfills the axioms). To make states subtractable, we use a _Grothendieck group construction_, which is a consistent way to add enough negatives to an algebraic structure that everything has an inverse (just like negative numbers were abstracted from natural numbers as "numbers that don't actually exist, but are useful for calculations"). In the end, the physical states of knowledge are a convex subset of a vector space.
   
I see two **applications:**

   1. We will be able to describe the problem of finding an optimal strategy to manipulate or learn about the environment as a **convex optimization problem** --- and hopefully use duality to find lower bounds for the agent's ability to do so. We can describe results from quantum complexity theory, in particular [this paper by Barnum-Saks-Szegedy](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.113.1101&rep=rep1&type=pdf) and the related [quantum adversary bound-universal query algorithm duality](https://arxiv.org/abs/1504.06943), in our formalism --- and plugging in the definitions, the convex optimization problems therein generalize to classical or decoherent-quantum situations. However, compared to the coherent-quantum case, I see no good bounds on the dimension of the involved vector spaces in the classical or decoherent-quantum case.[^2]
   2. Leaving mathematical rigour a bit (as we don't define infinite sums or limits, at least in this note), we can also describe **formal differential equations** and construct power series that describe e.g. the evolution of a learner's knowledge over time when it observes a Poisson process generating experimental data. This will involve a notion of multiplication of "states of knowledge", corresponding to an agent having access to multiple states of knowledge in parallel (e.g. multiple observations gathered at different times). So in fact, we are dealing with something called an $\mathbb{R}$-algebra (which we can always interpret as a vector space as well).

# Outline
In Section [intuition/alternative approach] - Section [modding out], I develop the concrete situations of classical, pure quantum, and mixed quantum physics and obtain structures $\mathcal{S}^\pm_{\mathrm{class}},$ $\mathcal{S}^\pm_{\mathrm{quant}},$ $\mathcal{S}^\pm_{\mathrm{decoh}}.$ More precisely, Section [intuition] explains how to imagine the algebras of knowledge for classical and pure quantum theory, Section [sets before modding out the equivalence relation] defines sets, Section [set inclusions (injective maps)] - Section [operations] structure on that sets, and Section [modding out] discusses the equivalence relation and basic algebraic structure of $\mathcal{S}^\pm$.

Section [determining the bias of a coin] is a basic example, Subsection [differential equation of knowledge] introduces a new concept: Formal differential equations of knowledge.

Section [convex optimization] - Section [an agent interacting with its environment] discuss convex optimization problems to help understand the "statics" and "dynamics" of states of knowledge better --- answering questions like

- "What functions of the environment can I calculate with a given maximal error probability and state of knowledge?",
- "How similar are two states of knowledge?",
- "Which states of knowledge are achievable by an agents in $N$ interaction rounds with its environment?", or
- "How many steps does an agent need to accomplish a task"?

Finally, Section [References] gives a literature overview.
!!! Tip
    If you want to speed up reading or don't know quantum physics, you can skip all mentions of non-classical situations. You can also try skipping the technical details and going to the example in Subsection [determining the bias of a coin] directly.

# Intuition
## For classical probability theory
In the concrete situation of classical probability theory, a "state of knowledge" essentially corresponds to a probability distribution over beliefs --- probability distributions conditioned on the internal memory state --- of the agent:
1. Elements are measures (i.e. probability distributions minus the normalization constraint) over probability distributions over the environment's states. So the agent has some Bayesian beliefs about the environment's state (a probability distribution over $E$ --- a member of the probability simplex $\Delta^E$) --- and which beliefs it currently has is itself random under some probability distribution.
2. Addition is addition of these measures (and convex mixtures correspond to probabilistic combinations),
3. Multiplication corresponds to Bayesian updates on new information: If the agent's current belief is a probability vector over $E$ $\vec{p_1}\in \Delta^E$, and it observes an event with probability distribution $\vec{p_2}$, the posterior probability it assigns to environmental state $e\in E$ should be
   $$(\vec{p_1}*\vec{p_2})_e:=\frac{(\vec{p_1})_e(\vec{p_2})_e}{\sum_{e'\in E} (\vec{p_1})_{e'}(\vec{p_2})_{e'}},$$

   i.e. the result of multiplying the probabilities and renormalizing.

As the agent's beliefs and the observed events are themselves randomized (i.e. probability measures), we take the pushforward measure of these probability measures under the operation (i.e. calculate the probability distribution over the resulting probability estimates).

We can show that this multiplication (on probability measures) is bilinear, associative and commutative.

Technically, the states of knowledge constructed below will always have finite support, i.e. the set of conditional probability distributions with nonzero probability density is nonzero.

## For pure quantum theory
In quantum probability theory, the states of knowledge correspond to the Gram matrices or --- equivalently reduced density matrices of "super-states" composed of the quantum states corresponding to individual environmental states. This is because for any bipartite quantum system $\mathcal{E}\otimes\mathcal{M}$, the set of nonnormalized density matrices on $\mathcal{E}$ is in 1-to-1 correspondence with the set of equivalence classes of pure states on $\mathcal{E}\otimes \mathcal{M}$, modulo local unitary transformations on $\mathcal{M}$: Every density matrix can be purified, local unitaries on the purifying space don't change the reduced density matrix, and [all purifications of a given reduced density matrix are related by a local unitary](https://quantumcomputing.stackexchange.com/questions/9368/why-do-purifications-only-differ-by-a-local-unitary).

One can verify that addition and multiplication of states of knowledge corresponds to entrywise addition and multiplication of these density matrices.

# Sets before modding out the equivalence relation
Consider an agent within an environment that could be in a state $e\in E,$ with some internal memory state $m\in M.$ For a fixed $E$, we define sets of joint, unnormalized "generalized probability destributions", which will turn into our desired notions of "states of knowledge" after modding out equivalence relation in section 5. We can do this for classical probability theory (probability vectors), pure quantum states (quantum wavefunctions), and mixed quantum states (quantum wavefunctions with an additional "decohering space")):[^3]

$$\left.\begin{array}
\mathcal{S}'^E_{\mathrm{class}}\\
\mathcal{S}'^E_{\mathrm{quant}}\\
\mathcal{S}'^E_{\mathrm{decoh}}
\end{array}\right\}:=
\left\{\begin{array}
((M,P)\\
(M,P)\\
(D,M,P)
\end{array}
\middle \vert \lvert M \rvert, \lvert D \rvert <\infty, P\in\left\{
\begin{array}
((\mathbb{R}^+)^{E\times M}\\
\mathbb{C}^{E\times M}\\
\mathbb{C}^{D\times E\times M}
\end{array}\right\}
\right\}.$$

$\mathbb{R}^+$ denotes the **nonnegative** real numbers. Note that

1. we do not fix $M$, or limit its sizes beyond requiring it is finite, and
2. we don't enforce normalization of our distributions.

We leave out the superscript $E$ and/or the subscript $\mathrm{class},$ $\mathrm{quant},$ $\mathrm{decoh}$ if they are irrelevant.

We define $\mathcal{S}'^\pm:=\mathcal{S}'\times\mathcal{S}'$ (for classical, coherent-quantum, or decohering-quantum states). Denote a tuple $(S'_1,S'_2)\in\mathcal{S}'^\pm$ by $S'_1-S'_2$ and interpret it as a formal difference accordingly.

As discussed in the outline, we spend the next sections defining more structure on the sets before modding out the right equivalence relation in Section [modding out].

# Set inclusions (injective maps)
We define maps that will play the role of inclusion maps after our equivalence relation (i.e. allow us to consider some sets as subsets of other sets --- will be/stay injections and form nothing but commutative diagrams when concatenated):

## Classical and pure quantum as mixed quantum states
The inclusions are

1. $\mathcal{S}'_\mathrm{class}\to\mathcal{S}'_\mathrm{decoh},$ $(M,P)\to( (P_{e,m}\delta_{e,e'}\delta_{m,m'})_{(e,m),e',m'})$ (in words, treating a classical as a completely decohered state),
2. $\mathcal{S}'_\mathrm{quant}\to\mathcal{S}'_\mathrm{decoh},$ $(M,P)\to((P_{e,m}P^*_{e',m'})_{(e,m),(e',m')})$ (in words, treating a pure quantum state as a quantum state with no decoherence).

## Knowledge as quasiknowledge
$$\mathcal{S}'\to\mathcal{S}'^\pm,$$ $$S'\to S'-0.$$

## Elements of E, vectors
We want to consider $e\in E$ as the state of knowledge in which the environment is guaranteed to be in state $e,$ with probability $1.$

1. $E\to\mathcal{S}'_\mathrm{class},$ $e\to(\{0\},(\delta_{e',e})_{(e',0)\in E\times \{0\}}))$

(where $\{0\}$ is an arbitrary 1-element memory space). The situation is the same in the pure-quantum or mixed-quantum case.

Similarly, we define TODO

\begin{align}
(\mathbb{R}^+)^E\to\mathcal{S}'&,~ \mathbf{v}\to(\{0\},(v_e)_{(e,0)\in E\times \{0\}})),\\
(\mathbb{R}^+)^E\to\mathcal{S}'&,~ \mathbf{v}\to(\{0\},(\sqrt{v_e})_{(e,0)\in E\times \{0\}}))
\end{align}


\begin{align}
(\mathbb{R}^+)^E\to\mathcal{S}'_\mathrm{class}&,~ \mathbf{v}\to(\{0\},(v_e)_{(e,0)\in E\times \{0\}})),\\
(\mathbb{R}^+)^E\to\mathcal{S}'_\mathrm{quant}&,~ \mathbf{v}\to(\{0\},(\sqrt{v_e})_{(e,0)\in E\times \{0\}}))
\end{align}

to define prior probability distributions with no additional knowledge, though this **won't** become a homomorphism of vector spaces in Section [modding out]. Again, we can do similar things for pure-quantum (taking square roots of the probabilities) and mixed-quantum states, but here, the pure-quantum vector considered as a mixed-quantum inclusion is not the same as the mixed-quantum inclusion, unfortunately.

## Real numbers
$\mathbb{R}^+\to \mathcal{S}'_\mathrm{class}$ maps $r$ to an all-$r$ matrix, again with trivial memory space. $\mathbb{R}^+\to  \mathcal{S}'_\mathrm{quant},$ $\mathcal{S}'_\mathrm{decoh}$ map to an all-$\sqrt{r}$ matrix with trivial memory space.

We embed real numbers $-r'<0$ in $\mathcal{S}'^\pm$ as $0-r',$ i.e. as the negation of the embedding of $r'.$

# Operations
We now define some operations on the $\mathcal{S}'$ and $\mathcal{S}'^\pm.$
## Addition
**Addition,** $+,$ is to be interpreted as the environment+agent being in either of the situations that the summands describe, and defined with **direct sums**:
1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_1)+(M_2,P_2):=(M_1\biguplus M_2, P'),$ with $M_1\biguplus M_2$ denoting the disjoint union of $M_1$ and $M_2$ and the elements of $P'$ composed of the elements of $P_1$ and $P_2.$
2. In $\mathcal{S}'_{\mathrm{quant}},$ the definition is equivalent.
3. In $\mathcal{S}'_{\mathrm{decoh}},$ $(D_1,M_1,\Psi_1) + (D_2,M_2,\Psi_2) := (D_1 \biguplus D_2, M_1 \biguplus M_2, \Psi'),$ with the entries of $\Psi'$ taken from $\Psi'_1,\Psi'_2$ if $d$ and $m$ belong to the same summand and set to $0$ otherwise.

In the $\mathcal{S}'^\pm,$ $(A-B) + (C-D) := ((A+C) - (B+D))$ as expected.

## Multiplication
We denote **multiplication** on the $\mathcal{S}'$ by concatenating the factors and interpret it as the model having access to two pieces of knowledge independently. That is:
1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_1)(M_2,P_2) := (M_1 \times M_2, P')$ with $P'_{e, (m_1, m_2)} := (P_1)_{e, m_1} (P_2)_{e, m_2},$
2. equivalently in $\mathcal{S}'_{\mathrm{quant}},$
3. and in $\mathcal{S}'_{\mathrm{decoh}},$ $(D_1,M_1,\Psi_1) (D_2,M_2,\Psi_2) := (D_1 \times D_2, M_1 \times M_2, \Psi')$ with $\Psi'_{(d_1, d_2), e, (m_1, m_2)} := (\Psi_1)_{d_1, e, m_1}(\Psi_2)_{d_2, e, m_2}.$

In the $\mathcal{S}'^\pm,$ $(A-B)(C-D) := (AC+BD) - (AD+BC).$

## Trace: Total probability
We define a linear map $\mathrm{tr}\colon \mathcal{S}'^\pm\to \mathbb{R}$ that restricts to $\mathcal{S}'\to\mathbb{R}^+$ as the total probability associated with a SOK: Using the [Schatten norms that treat the matrix entries as long vectors](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1116582616#L2,1_and_Lp,q_norms), we map

1. $(M, P)\to {\left\Vert P \right\Vert}_{1,1}$ on $\mathcal{S}'_{\mathrm{class}}$ (where ${\left\Vert P \right\Vert}_{1,1}$ is the [sum of entries](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1116582616#L2,1_and_Lp,q_norms)),
2. $(M, \Psi)\to {\left\Vert \Psi \right\Vert}^2$ on $\mathcal{S}'_{\mathrm{quant}}$,
3. $(D, M, \Psi)\to {\left\Vert \Psi \right\Vert}^2$ on $\mathcal{S}'_{\mathrm{quant}}$,

To define the trace on $\mathcal{S}'^\pm$, we map $(S'_1-S'_2)\to \mathrm{tr} S'_1-\mathrm{tr} S'_2$.

## The partial trace: Input from environment
Remember that when we consider different possible environments, we denote our sets by $\mathcal{S}'^E$ and similar. Considering a bipartite environment $E\times C,$ we define a **"partial trace"** $\mathrm{tr}_C \colon \mathcal{S}'^{E\times C}\to \mathcal{S}'^E.$ This works by considering the $C$ register, which previously was part of the environment, as part of the memory: it maps $M\to M\times C,$ copies the entries of $P$ or $\Psi$ and (in the decohering case) leaves $D$ untouched. After modding out an equivalence relation in section 6, this will look more like the partial trace --- after making some information accessible to the agent, the state of knowledge becomes equivalent with respect to all transformations that it can perform on that information.[^7]

Of course, on $\mathcal{S}'^\pm,$ $\mathrm{tr}_C (A-B):= \mathrm{tr}_C A - \mathrm{tr}_C B.$

The partial trace will be useful to discuss the interface between agent and the environment, i.e. discuss the possibility that the agent makes observations or performs actions that influence the next system state.

!!! Tip
    After the equivalence relation of Section [modding out], the set inclusion $\mathbb{R}\subseteq \mathcal{S}^{\pm\{0\}}$ defined in Subsection [real numbers] will be a _surjective_ vector space isomorphism for a 1-element $E=\{0\}.$ Under that isomorphism, the result of treating an environment $E$ as $\{0\}\times E$ and calculates the partial trace over $E$ will be consistent with the trace of Subsection [trace: total probability], as we expect from quantum physics.

## Sub-preimage of the partial trace: Output to environment
The partial trace corresponds to the agent incorporating part of the environment into its internal memory, as part of a measurement process. The reverse process - in which 

## The preorder
We define a **preorder** $S_1 \leq S_2$ to capture the notion that the agent can trivially transform a state into another state, as follows:
1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_2 T^T) \leq (M_2,P_2)$ for all trans formation matrices on the memory $T \in {(\mathbb{R}^+)}^{M_1 \times M_2}$ with $\Vert T\Vert_1\leq 1.$ $P_2 T^T$ is the usual matrix product of $P_2$ with the transpose of $T.$ One can check that this corresponds to applying $T$ on the $M$ subsystem; this is an awkward technicality related to us not using graphical notation. The latter is the [1-norm of $T,$ i.e. the maximal column sum.](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms) This means that starting from any state $m_2\in M_2,$ the probabilities of transitioning to a final state must sum to **at most** one; if it is $<1$ for some column, we interpret this as the agent giving up with a certain probability. If _all_ column sums were $1,$ we'd get a "proper" transition matrix that doesn't lose probability mass.

2. The definition in $\mathcal{S}'_{\mathrm{quant}}$ is analogous (with $\mathbb{R}^+$ replaced by $\mathbb{C}$) except that our constraint on $T$ is that $\Vert T\Vert_2\leq 1,$ using the [2-norm of $T$](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms). This means that the maximal singular value of $T$ is $\leq 1.$ Analogously to 1., _all_ singular values being $1$ is equivalent to $T$ being an isometry, i.e. a "proper" quantum transition matrix. If some singular values of $T$ are $<1,$ the block-matrix $\begin{pmatrix}T\\\\ \sqrt{I-T^\dagger  T}\end{pmatrix}$ is an isometry nevertheless; we can imagine the agent gives up if it lands in the lower block of that matrix and/or consider $T$ to be one [Kraus operator of a quantum channel](https://en.wikipedia.org/w/index.php?title=Quantum_operation&oldid=1094787035#Statement_of_the_theorem).

3. In $\mathcal{S}'_{\mathrm{decoh}},$ we want to consider transitions involving both $D$ and the memory. So we have $(D_1,M_1, T_D(\Psi_2 T^T_M)) \leq (D_2, M_2, \Psi_2),$ where $T_D(\Psi_2 T_M^T)$ denotes
   1. The application of some $T_M \in \mathbb{C}^{M_2 \times (M_1 \times D_M)}$ to the memory space of $\Psi_2,$ where $D_M$ is an intermediate decohering space and $\Vert T_M\Vert_2\leq 1,$
   2. following that, the application of $T_D\in \mathbb{C}^{D_1\times (D\times D_M)}$ to $D$ and $D_M,$ with $\Vert T_D\Vert_2\leq 1$ as well. So $T_M$ transforms the memory and generates a register $D_M$ that becomes part of the decohering space, and $T_D$ is an arbitrary transformation of that decohering space, which is **not necessarily an isometry either**.[^9]

On $\mathcal{S}'^\pm,$ $(A-B)\leq (C-D)$ iff $A+D\leq B+D.$

# Modding out
This section contains the technical details of the writeup's central claim --- $\mathcal{S}\subseteq\mathcal{S}^\pm,$ defined by modding out the equivalence relation given by the condition $S'_1\leq S'_2 \leq S'_1$, is a **convex cone as a subset of an associative, commutative $\mathbb{R}$-algebra that is ordered w.r.t. $\leq$ as a vector space, and the partial trace $\mathrm{tr}_C$ is a monotonous vector space homomorphism that is surjective when restricted to $\mathcal{S}^{E \times C}\to \mathcal{S}^E.$**
## The cancellation property
The following property is important. The proof of the harder direction is similar to the universal query algorithm in Subsection [the universal query algorithm].
!!! Info: Theorem: Cancellation property
    Let $S'_1,S'_2,S'_3\in \mathcal{S}'^\pm$. Then $S'_1+S'_3\leq S'_2+S'_3$ if and only if $S'_1\leq S'_2$.

!!! Tip: Proof
    We prove the statement for $S'_1,S'_2,S'_3\in\mathcal{S}'$, generalizing to $\mathcal{S}'^\pm$ is easy.
	
    If $S'_1\leq S'_2$ by some transformation $P$, we can construct a transformation witnessing $S'_1+S'_3\leq S'_2+S'_3$ by applying $P$ to $S'_2$ and identity to $S'_3$.

    The converse is more interesting. First, note that for any $A,B,C\in\mathcal{S}'$, $A+B\leq B+A$, and $(A+B)+C\leq A+(B+C)$, by operations that relabel the indices. Furthermore, note that $N S'\leq\sum^{N}_{i=1}S'\leq NS'$ for all $S'\in \mathcal{S}'$ and integers $N\geq 1$.
	
    Now suppose that $S'_1+S'_3\leq S'_2+S'_3$. By the last two paragraphs, this implies that $S'_1+S'_3\leq S'_3+S'_2$, and we can ignore brackets. Applying this inequality $N$ times, we obtain

    $$N S'_1+S'_3\leq S'_3+\sum^{N-1}_{i=0} S'_1 \leq\sum^{N-1}_{i=0} S'_2 + S'_3 \leq N S'_2 + S'_3.$$
	
    After rescaling, we obtain $S'_1+\frac{S'_3}{N}\leq S'_2+\frac{S'_3}{N}$ for any positive integer $N$. Applying the transformation witnessing this inequality to $S'_2$, we obtain a state arbitrarily close to $S'_1$ with respect to a reasonable metric. Therefore, $S_1'$ is reachable as well by a completeness argument:
    1. For given memory spaces, the set of states $S'$ achievable from $S'_2$ is the image of the continuous evaluation map at $S'_2$ of the compact set of transformations,
    2. therefore it is compact,
    3. a compact metric space is complete,
    4. so the limit of the Cauchy sequence approaching $S'_1$ is achievable as well.

## The equivalence relation and algebra
Finally, we define an **equivalence relation** on the $\mathcal{S}'$ and $\mathcal{S}'^\pm$ by $X\sim Y\leftrightarrow (X\leq Y \leq X),$ and call the equivalence classes the spaces of **states of knowledge** $\mathcal{S}$ and **states of quasiknowledge** $\mathcal{S}^\pm.$[^10] We can easily show this is an equivalence relation on the $\mathcal{S}'$, and using the cancellation property of the last section, we can show that it is an equivalence relation on $\mathcal{S}'^\pm$ as well.

It is tedious but mostly straightforward to check that all our operations behave well with this equivalence relation and the claim in the beginning of Section [modding out], that $\mathcal{S}\subseteq\mathcal{S}^\pm$ becomes a convex cone as a subset of an associative, commutative $\mathbb{R}$-algebra that is ordered w.r.t. $\leq$ as a vector space. This means:

1. For all operations, choosing different representatives of the same equivalence class leads to equivalent results, 
2. $\mathcal{S}^\pm$ is a commutative ring, fulfilling the ring axioms enumerated [here,](https://en.wikipedia.org/w/index.php?title=Ring_\(mathematics\)&oldid=1107386958#Definition) as well as the axiom that $*$ is commutative. Copying the content of the link, for any $A,B,C\in\mathcal{S}^\pm,$
   1. $(\mathcal{S}^\pm,+)$ is an Abelian (commutative) group, i.e.
	   1. $+$ is associative, $A+(B+C)=(A+B)+C,$
	   2. $+$ is commutative (abelian), $A+B=B+A,$
	   3. There is a neutral element $0$ such that $A+0=A,$
	   4. each $A$ has an inverse $-A$ such that $A+(-A)=0.$
   2. $(\mathcal{S}^\pm,*)$ is a monoid with neutral element $1$, i.e. 
	   1. $*$ is associative as above,
	   2. $*$ is commutative as above (this is what is meant by "commutative ring"),
	   3. $1*a=a*1=a$ for all $a\in\mathcal{S}^\pm$.
   3. the distributive law holds, i.e. $A(B+C)=(AB)+(AC);$ $(A+B)C=(AC)+(BC).$
3. $\mathcal{S}^\pm$ is an associative $\mathbb{R}$-algebra, [meaning](https://en.wikipedia.org/w/index.php?title=Associative_algebra&oldid=1115059242#From_ring_homomorphisms) that the embedding map $\mathbb{R}\to\mathcal{S}^\pm$ is a ring homomorphism ($f(a+b)=f(a)+f(b)$, $f(1)=1$ and $f(ab)=f(a)f(b)$); furthermore, the image lies in the center (i.e. for any $r\in \mathbb{R}$, $f(r)$ commutes with all $S\in\mathcal{S}^\pm$). These follow from the definitions.
4. The map $\mathcal{S}\to \mathcal{S}^\pm$, $s\to (s,0)$ is injective (which is necessary to be able to consider $\mathcal{S}$ as a subset of $\mathcal{S}^\pm$. This is the cancellation property of Section [the cancellation property].
5. $\mathcal{S}^\pm$ is ordered w.r.t. $\leq$ as a vector space. This [means that](https://en.wikipedia.org/w/index.php?title=Ordered_vector_space&oldid=1110116026#Definition) $S_1\leq S_2$ implies $S_1+S_3\leq S_2+S_3$, and $S_1\leq S_2$ implies $\lambda S_1\leq \lambda S_2$ \forall $y\in\mathbb{R}^+$. This can be verified easily using the cancellation property.
!!! Info: Question
    Is it an [ordered algebra](https://en.wikipedia.org/w/index.php?title=Ordered_algebra&oldid=1041753564) as well? That seems to be true for $\mathcal{S}^\pm_\mathrm{quant}$ (as can be seen from the Gram matrix representation), but I don't know whether the product of positive states is always positive in the classical or faulty-quantum situations that $0\leq 1$, the product of positive elements is positive.
    
    Using the equivalence discussed [here](https://en.wikipedia.org/w/index.php?title=Ordered_vector_space&oldid=1110116026#Positive_cones_and_their_equivalence_to_orderings), it may be more helpful to define the $S_1\leq S_2$ by $S_2-S_1\in \mathcal{S}$. Then products of positive elements are positive, and we can show using the trace that the vector space is Archimedean.

6. $\mathcal{S}$ is a [convex cone](en.wikipedia.org/w/index.php?title=Convex_cone&oldid=1114159935): $\mathcal{S}$ is closed under convex combinations and scalar multiplications.

The partial trace $\mathrm{tr}_C\colon\mathcal{S}^{\pm (E\times C)}\to \mathcal{S}^{\pm E}$ is a **linear map**[^14] that surjectively maps states of knowledge to states of knowledge (i.e. the image $\mathrm{tr}_C(\mathcal{S}^{E\times C})=\mathcal{S}^{E}$). Conversely, however, the preimage of a SOK _does_ generally contain non-SOKs. $\mathrm{tr}_C$ is also monotonous in $\leq.$

# Two examples
These two examples are supposed to illustrate this approach. The second one, of Subsection [differential equation of knowledge], introduces a new concept as well: Formal power series and differential equations of knowledge.
## Determining the bias of a coin
God has chosen a biased (classical) coin which either shows heads with $p=0.6$ or tails with $p=0.6,$ the prior probabilities for each are equal. The "agent" (in this simple example) can't make any decisions to influence the environment, it only observes a sequence of coin flips.

The environment is a 2-element set $E=\{\mathrm{HeadsBiased},\mathrm{TailsBiased}\};$ denote this by $E=\{0,1\}$ for simplicity. One representative for the initial state of knowledge $S_0$ is $(M,P)$ with $M=\{0\}$ and $P=\begin{pmatrix}1/2 \\1/2\end{pmatrix}=\frac{1}{2}\mathbf{1}.$ This corresponds to the agent having a trivial internal memory.

The agent could perform some internal memory transformations. For example, it could introduce a  3-state memory $M'=\{0,1,2\},$ and randomly choose $0$ with probability $1/2,$ and $1$ or $2$ with probability $1/4$ each. Then we'd get a representative $(M', P')$ with $P':=\begin{pmatrix}
\frac{1}{4} & \frac{1}{8} & \frac{1}{8} \\
\frac{1}{4} & \frac{1}{8} & \frac{1}{8} 
\end{pmatrix}.$ By our definitions, these representatives are equivalent as states of knowledge:

\begin{align*}
P'=P T^T,~~ & T:=\begin{pmatrix} \frac{1}{2} \\ \frac{1}{4} \\ \frac{1}{4} \end{pmatrix},\\
P=P'T'^T,~~ & T':=\begin{pmatrix} 1 & 1 & 1\end{pmatrix},
\end{align*}

and both $T$ and $T'$ have $1$-norm (maximal column sums) $\leq 1.$ The latter transformation matrix corresponds to forgetting $M'$ and getting back to a trivial memory state. So as we hoped, we have modded out equivalent transformations that the agent could perform on its internal memory.

The situation becomes more interesting after the agent made a few observations of coin tosses. Suppose it makes a single observation and stores the result as a memory state in $M:=\{h,t\}.$ Then the joint probability matrix becomes

$$P:=\begin{pmatrix} \frac{0.6}{2} & \frac{0.4}{2} \\ \frac{0.4}{2} &\frac{0.6}{2} \end{pmatrix},$$

and a representative of our state of knowledge is $(M,P).$

After it made $2$ observations (and stored both results), a representative is $(M_2,P_2)$ with $M_2:=\{hh,ht,th,tt\}$ and

$$P_2:=\begin{pmatrix}
\frac{0.6*0.6}{2} & \frac{0.6*0.4}{2} & \frac{0.4*0.6}{2} &\frac{0.4*0.4}{2}\\
\frac{0.4*0.4}{2} & \frac{0.4*0.6}{2} & \frac{0.6*0.4}{2} &\frac{0.6*0.6}{2}
\end{pmatrix}:$$

The internal memory can store all combinations of outcomes, and standard probability theory yields the joint probabilities.

We know that it is superfluous to store all outcomes: It suffices to keep track of _how many times_ the coin showed heads, as the order doesn't give additional information about which coin was chosen. We will now see that the formalism reflects this, as a representative for the latter form of storage is equivalent as a state of knowledge: When counting the coin flips, the joint probability matrix for observing a given numbers of heads is given by binomial distributions, i.e. we get a representative $(\{0,1,2\}, P_2')$ with

$$P_2':=\begin{pmatrix}
0.18 & 0.24 & 0.08\\
0.08 & 0.24 & 0.18
\end{pmatrix}.$$

We find transformation matrices with column sums $\leq 1$ such that $P_2'=P_2 T_2^T$ and $P_2=P_2' T_2'^T,$ proving that these representatives are equivalent:

$$T_2:=\begin{pmatrix}
1 & 0 & 0 & 0\\
0 & 1 & 1 & 0\\
0 & 0 & 0 & 1
\end{pmatrix},$$

$$T_2':=\begin{pmatrix}
1 & 0 & 0\\
0 & 0.5 & 0 \\
0 & 0.5 & 0\\
0 & 0 & 1\end{pmatrix}.$$

$T_2$ corresponds to counting the number of heads; $T_2'$ corresponds to randomly generating an order of flips given a count.

In general, given a SOK $S\in\mathcal{S},$ observing an additional coin flip and storing the result corresponds to multiplying $S$ by a SOK $Q$ represented by $(M_Q,P_Q):=\left(\{h,t\},\begin{pmatrix}0.6 & 0.4\\
0.4 & 0.6\end{pmatrix}\right)$ according to our formalism. In other words, if $S$ is represented by $(M,P),$ it gets mapped to the equivalence class of $(M\times M_Q, P')$ with $P'_{e,(m,x)}:= P_{e,m}(P_Q)_{e,x}.$ So after $n$ coin flips, the state of knowledge is

$$S=Q^n S_0.$$

Finally, suppose that in each step, the agent only observes a coin flip with probability $1/4,$ and doesn't change its state of knowledge otherwise. So it gets one of the outcomes $M_p:=\{h,t,n\},$ with a conditional probability matrix

$$\begin{pmatrix}0.15 & 0.10 & 0.75\\0.10 & 0.15 & 0.75\end{pmatrix}=\frac{1}{4}\begin{pmatrix}0.6 & 0.4\\0.4 & 0.6\end{pmatrix}\oplus\frac{3}{4}\begin{pmatrix}1\\1 \end{pmatrix}=\frac{1}{4}Q+\frac{3}{4}\mathbf{1}.$$

Here, the $\oplus$ signs stand for concatenating the columns, just like in our definition of addition. Then the state of knowledge after $n$ events (that are coin flips with probability $1/4$) is

$$\left(\frac{1}{4}Q + \frac{3}{4}\mathbf{1}\right)^n S_0.$$

Similarly as in the situation above, one can show that it's sufficient for the agent to store a counter of heads and tails observed so far.

## Differential equation of knowledge
Together, addition and multiplication allows us to define formal power series of knowledge (we leave mathematical rigour for a moment, as we didn't define infinite sums or limits). For example, suppose our learner observes the stars with a telescope without making any choices. In an infinitesimal time $\Delta t\to 0,$ it observes a supernova with probability $r \Delta t,$ generating experimental data $A\in \mathcal{S}.$ Otherwise, it observes nothing. If $S(t)$ is the state of knowledge over time, we obtain

$$S(t+\Delta t)\to ((1-r \Delta t)\mathbf{1}+r\Delta t A) S(t).$$

This is solved by

$$S(t)=\exp((A-\mathbf{1})rt) S(0),$$

which is to be interpreted as the appropriate formal infinite power series. In fact, writing

$$K(t)=\exp(-rt)\exp(Art)S(0)=\sum^\infty_{k=0} \frac{\exp(-rt) (rt)^k}{k!} A^k S(0)$$

shows that the amount of knowledge obtained follows a Poisson distribution. Note that these equations contain minus signs, so our Grothendieck construction was probably helpful.
   
TODO: Maybe we can also define such a formal power series when the agent is allowed to make choices --- when considering _sets of attainable states of knowledge_ instead of _individual states of knowledge_.

# Convex optimization
We have defined the algebraic structure and given two concrete examples. As discussed in the outline, the next two sections will be devoted to understanding the "statics" and "dynamics" of states of knowledge better. The partial trace will play a central role in these.

All of these conditions will be **convex** conditions, and convex optimization problems, because the SOKs are convex and partial traces are linear. So when we **truncate to a finite basis, we should be able to use convex optimization and duality**[^17] to find optimal algorithms that allow the agent to determine some value or perform some manipulation of the environment. The SDPs obtained in that way are mostly generalizations of [Barnum-Saks-Szegedy](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.113.1101&rep=rep1&type=pdf) (which discussed this question for quantum wavefunctions), and the adversary bound - quantum query algorithm duality.

# Output conditions
## Average vs. variational measures
For the various output conditions, there will be various

## Measurements
Given $S\in\mathcal{S}^E,$ suppose that an agent outputs some result $s\in C$. This means that it produces an arbitrary state $S_C\in \mathcal{S}^{E\times C}$ with $\mathrm{tr}_C S_C \leq S$: Any such state is achievable by an appropriate transformation, and conversely, for any achievable $S_C$, incorporating the output $C$ back into the memory space must yield a state achievable from $S$. Then the joint "probability distribution" (in scare quotes because $S$ doesn't need to be normalized) is given by $\operatorname{eval}(S_C)\in (\mathbb{R}^+)^{E\times C}$ as defined in Subsection [total trace, evaluation map]. We therefore define the set of achievable joint probability distributions as

$$\operatorname{Meas}(S):=\left\{ \operatorname{eval}(S_C) \mid \mathrm{tr_C} S_C \leq S,~S_C \in \mathcal{S}^{E\times C} \right\} \subseteq (\mathbb{R}^+)^{E\times C}.$$

It is easy to turn this definition in optimization problems to find the optimal measurement. For example, suppose that $S$ is normalized, and define a "utility function" $V\in \mathbb{R}^{E\times C}$ - a matrix of payoffs if the environmental state is $E$, and the algorithm outputs $C$. For some function $f\colon E\to C$, for example, $V[e,c]=\delta_{e,f(c)}$ would correspond to the agent succeeding if it correctly determines this function of the environment. Then the optimization problem

\begin{align*}
&\underset{R\in\mathcal{S}^{C\times E},~\lambda\in\mathbb{R}^+}{\operatorname{maximize}}& & \lambda \\
&\operatorname{subject\ to}
& & \mathrm{tr_C}\left(c_S R\right) = S,\\
&&& \mathrm{tr_C}\left(c_T R\right) = T,\\
&&& \lambda\leq \mathrm{tr_E} R.
\end{align*}

an agent wants to calculate a function $f\colon E\to C.$ It produces some $S_C\in \mathcal{S}^{E\times C}$ with $\mathrm{tr}_C S_C \leq S.$ Given this $S_C,$ the "probability"  of getting output $C$ for input $E$ is $\mathrm{tr}\left( (E,C) S_C \right)$ (with $(E,C)$ interpreted as a member of $\mathcal{S}^{E\times C}$ as in Subsection [elements of e, vectors]): Multiplying by $(E,C)$ zeroes out all probabilities that don't correspond to that combination, and $\mathrm{tr}$ calculates the remaining probability.

## Distinguishing
For convenience, for $S\in\mathbb{S}^E$ a normalized state of knowledge, we define by $\mathrm{dist}(S)$ the maximum success probability of determining the environmental state. $E'$ is a copy of $E$, containing the same environmental states but labeled differently to avoid ambiguity. The optimization problem is

\begin{align*}
&\underset{W \in \mathcal{S}^{E\times E'}}{\operatorname{maximize}}& & \sum_{e \in E} \left(\operatorname{eval}(W)\right)[(e,e)] \\
&\operatorname{subject\ to}
& & \mathrm{tr_{E'}} W \leq S.
\end{align*}

## Fidelity of SOKs: Quantifying similarity
Consider two states of knowledge $S,T\in\mathcal{S}$ (not $\mathcal{S}^\pm$ so far --- I leave the right generalization open for now). Define a "control space" $C:=\left \{c_S, c_T \right\}$, and a "measurement space" $M:=\left \{m_S, m_T\right\}$. The optimal value of the following optimization problem quantifies how similar two states of knowledge are. In words, it answers the question "if the environment contained an additional bit, and the agent would be in SOK $S$ or $T$ conditioned on this bit's value, what is the minimal amount of information it can learn about the bit's value by learning the rest of the environment?" If the SOKs are equal, this should be $0$.

So we define the **fidelity** $F(S, T)$ by $0$ if $S=0$ or $T=0$, and the supremal value of the optimization problem

\begin{align*}
&\underset{R\in\mathcal{S}^{C\times E},~\lambda\in\mathbb{R}^+}{\operatorname{maximize}}& & \lambda \sqrt{\mathrm{tr} S \mathrm{T}}\\
&\operatorname{subject\ to}
& & \mathrm{tr_C}\left(c_S R\right) = S/\mathrm{tr} S,\\
&&& \mathrm{tr_C}\left(c_T R\right) = T/\mathrm{tr} T,\\
&&& \lambda\leq \mathrm{tr_E} R
\end{align*}

otherwise.

We also define the **Lee-Roland fidelity** $\tilde{F}(S,T)$ by

$$\tilde{F}(S,T):=\inf_{\mathrm{tr} W = 1, W\in\mathcal{S}^E} F(SW, TW)$$.

!!! Tip
    The definition is lifted from the semidefinite programming characterization of the fidelity of quantum states (see e.g. [here](https://sites.google.com/site/jamiesikora/teaching/semidefinite-programming-quantum-information), lecture 11), to which it reduces in the pure-quantum case.
	
	

We get the following result

# An agent interacting with its environment
## Evolution
Now consider an agent interacting with its environment. So we introduce some output and input subsystems $O,$ $I,$ and a "law of nature" $T\colon\mathcal{S}^{E\times O}\to \mathcal{S}^{E\times I}$ that evolves the environment influenced by the agent's choices, and generates measurement data. The "law of nature" should be instantiated as some transition matrix $T',$ similar to the ones in our order definition in Subsection [the preorder]:

1. For $\mathcal{S}_\mathrm{class},$ $T'\in(\mathbb{R}^+)^{(E\times I)\times(E\times O)}$ and $\Vert T'\Vert_1\leq 1,$
2. For $\mathcal{S}_\mathrm{quant},$ $T'\in\mathbb{C}^{(E\times I)\times(E\times O)}$ and $\Vert T'\Vert_2\leq 1,$
3. For $\mathcal{S}_\mathrm{decoh},$ $T'\in\mathbb{C}^{(D_T\times E\times I)\times(E\times O)}$ and $\Vert T'\Vert_2\leq 1,$ i.e. $T'$ may add some new space $D_T$ to the decohering space.

We can clearly form a tensor products of transformations from the product of their constituting matrices; denote this by $T_1 \otimes T_2$ as expected.

Then, starting with a SOK $S\in \mathcal{S}^{E},$ the states of knowledge attainable by the agent in one step are

$$\left\{ \mathrm{tr}_I (T(S_O)) \mid\mathrm{tr}_O S_O \leq S, S_O\in \mathcal{S}^{E\times O} \right\}:$$

If a state $S_O\in \mathcal{S}^{E\times O}$ can be obtained by an agent writing into the output register, based on $S\in \mathcal{S}^E$, then reincorporating $O$ in $E$ (i.e. applying the partial trace) would need to yield a state $\leq \mathcal{S}^E$. Conversely, all states fulfilling this can be obtained. Then the transformation corresponds to applying $T,$ and incorporating the next step's input into the internal memory is the same as application of the partial trace.

## The adversary bound
Consider a multi-step evolution with each step as in Subsection [evolution]: Starting from $0\neq S_0\in \mathcal{S}^E,$ the system goes through intermediate states $S_1,S_2,\ldots,S_{N-1}$ and arrives at a final state $S_N$ --- influenced by the agent, whose choices in the different steps are encoded by SOKs $S_{O,k}\in \mathcal{S}^{E\times O}:$

\begin{align*}
S_{k+1}	&= \mathrm{tr_I} (T(S_{O,k})),\\
\mathrm{tr_O} S_{O,k} &\leq S_k, S_{O,k}\in \mathcal{S}^{E\times O}
\end{align*}

with $k\in \{0,1,\ldots N-1\}$. By linearity, we can add these expressions and obtain

\begin{align*}
\sum_{k=1}^{N} S_k &= \mathrm{tr_I} \left(T\left(\sum_{k=0}^{N-1} S_{O,k} \right)\right),\\
\mathrm{tr_O}\left(\sum_{k=0}^{N-1} S_{O,k}\right) &\leq \sum_{k=0}^{N-1} S_{k}.
\end{align*}

We add $S_0$ on both sides of the equality, and $S_N$ on both sides of the inequality. Then we can combine these expressions:

$$S_N+\mathrm{tr}_O\left( \sum_{k=0}^{N-1}S_{O,k} \right) \leq \sum_{k=0}^{N} S_{k}=S_0+\mathrm{tr}_I \left(T\left( \sum^{N-1}_{k=0}S_{O,k} \right)\right).$$

Now let $\tilde{S}:=\sum_{k=0}^{N-1}S_{O,k}$. Then $\tilde{S}\in \mathcal{S}$. Furthermore, by induction, all $\mathrm{tr} S_{O,k}\leq \mathrm{tr} S_0$ --- implying $\mathrm{tr} \tilde{S} \leq N \mathrm{tr}{S_0}$, or $N \geq \mathrm{tr} \tilde{S}/\mathrm{tr}{S_0}.$ 

In particular, any algorithm performing the transformation $S_0\to S_N$ 
must take at least

$$ N\geq \left\lceil \frac{\mathrm{Adv}(S_N-S_0)}{\mathrm{tr}{S_0}} \right\rceil$$

steps, where we define $\mathrm{Adv}(S_N-S_0)$ as the infimal value of the optimization problem

\begin{align*}
&\underset{\tilde{S}\in\mathcal{S}^{E\times O}}{\operatorname{minimize}}& & \mathrm{tr} \tilde{S} \\
&\operatorname{subject\ to}
& & \mathrm{tr_I} \left(T\left(\tilde{S}\right)\right) - \mathrm{tr_O} \tilde{S} \geq S_N - S_0.
\end{align*}

As in the case of fidelity and trace distance, it makes sense to define a "variational version" of this bound if $S_0$ is a conditional probability distribution and we want to achieve a state transformation for all conceivable prior probabilities:

$$\widetilde{\mathrm{Adv}}(S_N-S_0):=\sup \left\{ \mathrm{Adv}(P(S_N-S_0))\mid \mathrm{tr}(P S_0)\leq 1, P\in \mathcal{S}^E \right\}.$$

Note that both $\mathrm{Adv}$ and $\widetilde{\mathrm{Adv}}$ only depend on $S_N-S_0$, and the scale invariance: For any $\lambda\in\mathbb{R}^+$,

$$\mathrm{Adv}(\lambda (S_N - S_0))=\lambda \mathrm{Adv}(S_N-S_0).$$

!!! Tip Side note
    We could slightly strengthen this bound by adding the constraint
    
    $$\mathrm{tr_O}\left(\tilde{S}\right)-S_0\in \mathcal{S},$$
    
    which must be true for $\tilde{S}$ obtained from algorithms as well. I didn't put this constraint in by default, as it would be irrelevant for $\mathrm{Adv}(S_N - S_0)\gg 1,$ make $\mathrm{Adv}$ dependent on $S_0$ explicitly (rather than just $S_N-S_0$) and break the scale invariance. 

## The universal query algorithm
Under the condition that the algorithm can always choose to do nothing, we can turn the feasible solutions of the optimization problems above into algorithms approximating the desired state transformation. This works similarly as the proof of the cancellation property in Subsection [the cancellation property], as mentioned there.

$${\large\forall} S \in \mathcal{S}^E ~ {\large\exists  S_{O,\mathrm{idle}}\in \mathcal{S}^{E\times O}} ~\colon~ \mathrm{tr_O} S_{O,\mathrm{idle}} = \mathrm{tr_O} \left( T \left( S_{O,\mathrm{idle}} \right) \right) = S.$$ 

Now apply this to $S:=S_0,$ $S:=S_N$, and call the resulting states $S_{O,0}$ and $S_{O,N}.$ Then the transformation given by the equations

Choosing a number of steps $N'$ and

$$S_{O,k} := (N' - k) S_{O,0} + k S_{O,N} + \tilde{S},~0\leq k\leq N'$$

results in a valid query algorithm as in the last Subsection [the adversary bound], transforming $N' S_0 + \tilde{S}$ into $N' S_N + \tilde{S}$. Up to normalization, this is equivalent to an algorithm transforming

$$S_0 + \frac{\tilde{S}}{N'} \rightarrow S_N  + \frac{\tilde{S}}{N'}$$

in $N'$ steps. As we let $N'\to 0$

We now turn feasible solutions of the optimization problems above into algorithms.

Namely, consider a feasible solution of the optimization problem for $\mathrm{Adv}$, $\tilde{S}.$ Then 
ToDO

A bit of intuition: Up to renormalizing (multiplying by $1/T$), this sum corresponds to a "random step" of the agent.

# References
TODO

# Acknowledgements
Thanks to [John Baez](https://math.ucr.edu/home//baez/README.html) for helpful comments.

# Footnotes
[^1]: Our convention is that each row is a different environmental state, and each column a different internal memory state.

[^2]: In fact, as I understand, they are infinite-dimensional.

[^3]: I think now that using density matrices and a variant of quantum channels would work as well to discuss mixed states.

[^7]: In the correspondence $\mathcal{S}^E_\mathrm{quant}$<->reduced density matrices on $E$, it should work out to be a literal partial trace.

[^9]: I think one can obtain a more elegant description of possible transformations by adapting the parallel developments by [Gutoski and Watrous](https://arxiv.org/pdf/quant-ph/0611234.pdf) and [Chiribella, D'Ariano, and Perinotti](https://journals.aps.org/pra/abstract/10.1103/PhysRevA.80.022339). But I don't see a use of that as of this note.

[^10]: If one looks into the definitions, one can see that the "states of quasiknowledge" construction is related to a [Grothendieck group construction](https://en.wikipedia.org/wiki/Grothendieck_group). I looked into Wikipedia to read that this has his name from a specific case "which resulted in the development of [K-theory](https://en.wikipedia.org/w/index.php?title=K-theory&oldid=1072713370)". What's written there looks formally quite similar to what I do here, though I don't understand it in detail. So maybe one can call these thoughts "K-theory on the space of probabilistic transformations?"

[^13]: A side remark: $\mathcal{S}_{\mathrm{quant}}\subset\mathcal{S}^\pm_{\mathrm{quant}}$ should be isomorphic to the algebra of complex positive semidefinite $E\times E$ matrices (as a subset of the Hermitian matrices), with elementwise addition and multiplication. There is a 1-to-1 correspondence between bipartite, unnormalized wavefunctions modulo unitary transformations on one part, and positive semidefinite matrices (which are the same as density matrices without normalization): Every density matrix can be purified, local unitaries on the purifying space don't change the reduced density matrix, and [all purifications of a given reduced density matrix are related by a local unitary](https://quantumcomputing.stackexchange.com/questions/9368/why-do-purifications-only-differ-by-a-local-unitary).

[^14]: It is **not** a homomorphism of $\mathbb{R}$-algebras.

[^17]: Apparently, [this Oberwolfach seminar](https://homepages.cwi.nl/~monique/ow-seminar-sdp/) contains a lecture on infinite-dimensional semidefinite optimization.
<!-- Markdeep: --><style class="fallback">body{visibility:hidden;white-space:pre;font-family:monospace}</style><script src="markdeep.min.js" charset="utf-8"></script><script src="https://morgan3d.github.io/markdeep/latest/markdeep.min.js" charset="utf-8"></script><script>window.alreadyProcessedMarkdeep||(document.body.style.visibility="visible")</script>
